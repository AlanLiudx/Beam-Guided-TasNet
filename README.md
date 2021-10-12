# Beam-Guided-TasNet
Beam-Guided TasNet: An Iterative Speech Separation Framework with Multi-Channel Output

Please refer to [Preprint Paper](https://arxiv.org/abs/2102.02998) 

## Contents
- [Beam-Guided-TasNet](#beam-guided-tasnet)
  - [Contents](#contents)
  - [Dataset](#dataset)
  - [Installation](#installation)
  - [Train](#train)
  - [Test](#test)
  - [Results](#results)
    - [Noncausal](#noncausal)
    - [Causal](#causal)
    - [Oracle test](#oracle-test)
  - [Acknowledgement](#acknowledgement)
  - [Citing us](#citing-us)

## Dataset
([↑up to contents](#contents))

We use dataset `spatialize_wsj0-mix`, which can be created following [[link]](https://www.merl.com/demos/deep-clustering)

## Installation
([↑up to contents](#contents))

This recipe requires `asteroid` and `asteroid-filterbanks`. 
The script `run.sh` will automatically download these requirements in the current directory. Some requirement may not be satisfied. The best way is to run this recipe directly and errors will tell the uninstalled modules.

Paths should be modified according to your environment : 
The json dataset
```bash
sed -i 's?/path/to/dataset?YOUR_PATH?g' data/2speakers/wav8k/*/*/*.json
```
The `run.sh` :
```bash
python_path=YOUR_PYTHON_PATH
```

## Train
([↑up to contents](#contents))

we recommend running :
```bash
./run.sh --id $id 
```

## Test
([↑up to contents](#contents))

You can give a quick test by running :
```bash
./run.sh --tag $tag --id $id --test_stage $test_stage --stage 5
```
You can give a strict check by running :
```bash
./run.sh --tag $tag --id $id --test_stage $test_stage --stage 6
```
Note that the inference results have differences between the quick test and the strict check under the causal condition due to different paddings.

## Results
([↑up to contents](#contents))

The following results are obtained using strict check `Stage=6`. We use acoustic models trained by the script from [SMS-WSJ](https://github.com/fgnt/sms_wsj). The decode script can be called like
```bash
./asr_decodespwsj2/run_decode.sh $ROOT/ConvTasNet_parcoder2_snr_serial3/exp/train_convtasnet_reverb2reverb_8kmin_823e6963noncausal/examples_strictcheck1bfs
```
We recommend you put `asr_decodespwsj2` into SMS-WSJ directory.

### Noncausal
| Stage 1 | Iteration # on Stage 2 | w/o last MVDR |     | w/ last MVDR |     |
|---------|------------------------|---------------|-----|--------------|-----|
|         |                        | SDR           | WER | SDR          | WER |
| Beam-TasNet |  | 12.652 | 22.11 | 17.387 | 13.38 |
| ✔ | 0 | 11.823 | 25.08 | 16.768 | 13.83 |
| ✔ | 1 | 18.738 | 13.50 | 19.181 | 12.39 |
| ✔ | 2 | 19.727 | 13.08 | 19.735 | **12.23** |
| ✔ | 3 | **19.930** | 12.98 | 19.645 | 12.37 |
| ✔ | 4 | 19.442 | 13.19 | 19.203 | 12.61 |

### Causal

| Stage 1 | Iteration # on Stage 2 | w/o last MVDR |     | w/ last MVDR |     |
|---------|------------------------|---------------|-----|--------------|-----|
|         |                        | SDR           | WER | SDR          | WER |
| ✔ | 0 |  8.607 | 35.63 | 10.849 | 22.67 |
| ✔ | 1 | 12.608 | 21.01 | 11.822 | 20.90 |
| ✔ | 2 | 13.237 | **20.01** | 11.918 | 20.72 |
| ✔ | 3 | **13.275** | 20.16 | 11.819 | 20.95 |
| ✔ | 4 | 13.166 | 20.43 | 11.673 | 21.37 |

### Oracle test
| Oracle method | Causal | w/o last MVDR |     | w/ last MVDR |     |
|---------------|--------|---------------|-----|--------------|-----|
|               |        | SDR           | WER | SDR          | WER |
| Signal | × | ∞ | 11.67 | 23.481 | 11.89 |
| Mask   | x | 11.004 | 28.09 | 14.458 | 15.75 |
| Mask-avg   | x | 11.004 | 28.09 | 14.711 | 15.01 |
| Signal | ✔ | ∞ | 11.67 | 17.977 | 13.18 |
| Mask   | ✔ | 11.004 | 28.09 | 10.557 | 20.85 |
| Mask-avg | ✔ | 11.004 | 28.09 | 8.637 | 23.24 |

## Acknowledgement
Thanks for `Asteroid` providing the basic training framework,
```BibTex
@inproceedings{Pariente2020Asteroid,
    title={Asteroid: the {PyTorch}-based audio source separation toolkit for researchers},
    author={Manuel Pariente and Samuele Cornell and Joris Cosentino and Sunit Sivasankaran and
            Efthymios Tzinis and Jens Heitkaemper and Michel Olvera and Fabian-Robert Stöter and
            Mathieu Hu and Juan M. Martín-Doñas and David Ditter and Ariel Frank and Antoine Deleforge
            and Emmanuel Vincent},
    year={2020},
    booktitle={Proc. Interspeech},
}
```
Thank for `ESPnet` with the MVDR code,
```BibTex
@inproceedings{li2020espnet,
  title={{ESPnet-SE}: End-to-End Speech Enhancement and Separation Toolkit Designed for {ASR} Integration},
  author={Chenda Li and Jing Shi and Wangyou Zhang and Aswin Shanmugam Subramanian and Xuankai Chang and Naoyuki Kamo and Moto Hira and Tomoki Hayashi and Christoph Boeddeker and Zhuo Chen and Shinji Watanabe},
  booktitle={Proceedings of IEEE Spoken Language Technology Workshop (SLT)},
  pages={785--792},
  year={2021},
  organization={IEEE},
}
```
Thanks for `SMS-WSJ` for the ASR training script,
```BibTex
@Article{SmsWsj19,
  author    = {Drude, Lukas and Heitkaemper, Jens and Boeddeker, Christoph and Haeb-Umbach, Reinhold},
  title     = {{SMS-WSJ}: Database, performance measures, and baseline recipe for multi-channel source separation and recognition},
  journal   = {arXiv preprint arXiv:1910.13934},
  year      = {2019},
}
```

## Citing us
([↑up to contents](#contents))

If you loved this idea and you want to cite us, use this :
```BibTex
@misc{chen2021beamguided,
      title={Beam-Guided TasNet: An Iterative Speech Separation Framework with Multi-Channel Output}, 
      author={Hangting Chen and Pengyuan Zhang},
      year={2021},
      eprint={2102.02998},
      archivePrefix={arXiv},
      primaryClass={eess.AS}
}
```
