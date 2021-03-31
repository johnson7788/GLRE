# Global-to-Local Neural Networks for Document-Level Relation Extraction
[![Contributions Welcome](https://img.shields.io/badge/Contributions-Welcome-brightgreen.svg?style=flat-square)](https://github.com/nju-websoft/GLRE/issues)
[![License](https://img.shields.io/badge/License-GPL-lightgrey.svg?style=flat-square)](https://github.com/nju-websoft/GLRE/blob/master/LICENSE)
[![language-python3](https://img.shields.io/badge/Language-Python3-blue.svg?style=flat-square)](https://www.python.org/)
[![made-with-Pytorch](https://img.shields.io/badge/Made%20with-Pytorch-orange.svg?style=flat-square)](https://pytorch.org/)

>关系抽取（RE）的目的是识别文本中命名实体之间的语义关系。
>近年来，关系抽取被提升到了文档层面，它需要对整个文档中的实体和提及进行复杂的推理。
>在本文中，我们提出了一种新颖的的文档级RE模型，通过将文档信息编码为实体全局和局部表示以及上下文关系表示。
>实体全局表示对文档中所有实体的语义信息进行建模，实体局部表示对特定实体的多次提及的上下文信息进行聚合，
>上下文关系表示则对其他关系的主题信息进行编码。实验结果表明，我们的模型在两个公共数据集上实现了文档级RE的优越性能。
>特别是在提取距离较远、有多个提及的实体之间的关系方面效果明显。

## Getting Started

### 代码文件结构说明
```
GLRE/
├─ configs/
    ├── cdr_basebert.yaml: "Train"设置下的CDR数据集配置文件。因为cdr的数据集较小，所以开发集在第二种配置中从训练集切分出一部分。
    ├── cdr_basebert_train+dev.yaml: 在 "Train+Dev "设置下，CDR数据集的配置文件。
    ├── docred_basebert.yaml: 在 "Train "设置下，DocRED数据集的配置文件。
├─ data/: 关于CDR和DocRED数据集的原始数据和预处理数据。
    ├── CDR/
    ├── DocRED/
├─ data_processing/: 数据预处理脚本
├─ results/: 预训练的模型和结果
├─ scripts/: 运行脚本
├─ src/
    ├── data/: 读取数据并转换为batch
    ├── models/: 核心模块实现GLRE 
    ├── nnet/: 子层实现GLRE
    ├── utils/: 功能函数
    ├── main.py
```

### Dependencies
  - python (>=3.6)
  - pytorch (>=1.5)
  - numpy (>=1.13.3)
  - recordtype (>=1.3)
  - yamlordereddictloader (>=0.4.0)
  - tabulate (>=0.8.7)
  - transformers (>=2.8.0)
  - scipy (>=1.4.1)
  - scikit-learn (>=0.22.1)

### Usage
#### 数据和预处理
数据集包括CDR和DocRED。数据分别位于`data/CDR`目录和`data/DocRED`目录。
预处理脚本位于`data_processing`目录，预处理结果分别位于`data/CDR/processed`目录和`data/DocRED/processed`目录。
预处理后的模型在`results`目录下。

我们预处理CDR数据集按照 [edge-oriented graph](https://github.com/fenchri/edge-oriented-graph):

    下载Genia Tagger和句子Splitter： 
    $ cd data_processing
    $ mkdir common && cd common
    $ wget http://www.nactem.ac.uk/y-matsu/geniass/geniass-1.00.tar.gz && tar xvzf geniass-1.00.tar.gz
    $ cd geniass/ && make && cd ..
    $ git clone https://github.com/bornabesic/genia-tagger-py.git
    $ cd genia-tagger-py 
    
    这里，你应该修改genia-tagger-py中的Makefile，并将第3行替换为 `wget http://www.nactem.ac.uk/GENIA/tagger/geniatagger-3.0.2.tar.gz`
    $ make
    $ cd ../../
    为了处理数据集，首先要将它们转换为PubTator格式。运行处理脚本如下。
    $ sh process_cdr.sh

然后，请使用以下代码对DocRED数据集进行预处理。  
    python docRedProcess.py --input_file ../data/DocRED/train_annotated.json \
                       --output_file ../data/DocRED/processed/train_annotated.data \
    
#### Train & Test
首先，你应该从以下地方下载biobert_base和bert_base. [figshare](https://doi.org/10.6084/m9.figshare.12967169) 并将它们放在GLRE目录中。

默认的超参数在 "configs "目录下，training&test脚本在 "scripts "目录下。
另外，"run_cdr_train+dev.py "脚本对应的是 "traing+dev "设置下的CDR。

    python scripts/run_cdr.py
    python scripts/run_cdr_train+dev.py
    python scripts/run_docred.py

#### Evaluation
对于CDR，您可以使用以下评估脚本来评估结果。

    python utils/evaluate_cdr.py --gold ../data/CDR/processed/test.gold --pred ../results/cdr-dev/cdr_basebert_full/test.preds --label 1:CID:2

For DocRED, you can submit the `result.json` to [Codalab](https://competitions.codalab.org/competitions/20717).

## License

This project is licensed under the GPL License - see the [LICENSE](https://github.com/nju-websoft/GLRE/blob/master/LICENSE) file for details.


## Citation

If you use this work or code, please kindly cite the following paper:

```
@inproceedings{GLRE,
 author = {Difeng Wang and Wei Hu and Ermei Cao and Weijian Sun},
 title = {Global-to-Local Neural Networks for Document-Level Relation Extraction},
 booktitle = {EMNLP},
 year = {2020},
}
```

## Contacts

If you have any questions, please feel free to contact [Difeng Wang](mailto:dfwang.nju@gmail.com), we will reply it as soon as possible.
