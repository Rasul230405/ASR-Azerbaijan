# az_stt_intern
# Azərbaycan Dili üçün Nitq Tanıma (ASR) Sistemi

## Layihənin Qısa İzahatı
Bu layihədə Azərbaycan dili üçün avtomatik nitq tanıma (ASR) sisteminin qurulmuşdur. Mövcud açıq mənbəli (open-source) ASR modeli əvvəlcə "zero-shot" rejimində test edilmiş, daha sonra isə məhdud resurslar nəzərə alınaraq **LoRA (Low-Rank Adaptation)** ilə fine-tune edilmişdir.

**Dataset:** [Mozilla Data Collective - Azerbaijani Dataset](https://mozilladatacollective.com/datasets/cmn29hqvk015ko107fblsr5ay)

## İstifadə Olunan Model və Parametrlər
Sistem resurslarına qənaət etmək məqsədilə training üçün aşağıdakı konfiqurasiyalar istifade edilmişdir:

* **Baza Modeli:** `openai/whisper-small` (~244M parametr)
* **Training Metodu:** LoRA (Low-Rank Adaptation)
* **LoRA Parametrləri:**
  * `r` (Rank): 8
  * `lora_alpha`: 32
  * `target_modules`: ["q_proj", "v_proj"]
  * `lora_dropout`: 0.05
* **Training Parametrləri:**
  * Epochs (`num_train_epochs`): 5
  * Batch size (`per_device_train_batch_size`): 2
  * Gradient Accumulation: 4 (effektiv olaraq batch size = 2 * 4 = 8)
  * Precision (`fp16`): True 
  * Learning Rate (`learning_rate`): 1e-3

## Nəticələr və Fine-Tuning Müqayisəsi
Test nəticələri:

| Model Növü | Word Error Rate (WER) | Character Error Rate (CER) |
| :--- | :---: | :---: |
| **Baza Modeli** (Whisper-Small) | 92.77% | 42.51% |
| **Fine-Tuned Model** (LoRA Adapter) | **56.74%** | **16.70%** |

**Müqayisə:** Baza modeli test edilərkən bəzi hallarda ərəb/fars hərflərinə tranksripsiya edərək "hallüsinasiya" xətaları vermiş və bu, ümumi WER göstəricisini artırmışdır. Modelin fine-tune edilməsi bu xətaları tamamilə aradan qaldırmışdır. CER göstəricisinin 42.51%-dən 16.70%-ə düşməsi modelin səsləri hərflərə çevirmək qabiliyyətinin əhəmiyyətli dərəcədə yaxşılaşdığını göstərir.

## Repository Strukturu
az-stt-intern/
├── README.md            # Qısa izahat + nəticələr
├── part_a/              # Hissə A 
├── part_b/              # Hissə B
├── results/             # WER/CER cədvəlləri, qrafikləri
├── report.pdf           # Hissə C
└── requirements.txt     # dependencies

## Kodu İşə Salmaq Üçün Addımlar (Setup & Run)

**1. Repository-ni klonlayın:**

SSH:
```bash
git clone git@github.com:Rasul230405/az_stt_intern.git
cd az_stt_intern
```

HTTPS:
```bash
git clone https://github.com/Rasul230405/az_stt_intern.git
cd az_stt_intern
```

**2. Virtual mühit yaradın və aktivləşdirin:**

Linux/MacOS üçün:
```bash
python3 -m venv venv
source venv/bin/activate
```

Windows üçün:
```bash
python3 -m venv venv
venv\Scripts\activate
```

**3. Tələb olunan dependency-ləri quraşdırın:**
```bash
pip install -r requirements.txt
```

**4. Skriptləri run etmək üçün:**

Baza modelinin testi (Hissə A):
```bash
python3 part_a/part_a.py
```

Fine-Tuning (Hissə B):

(Modeli training etmək və adapter yaratmaq üçün)
```bash
python3 part_b/part_b1_2.py
```

(Training olunmuş yeni modeli test etmək üçün)
```bash
python3 part_b/part_b3.py
```