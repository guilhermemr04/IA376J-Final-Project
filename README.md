# IA376J Final Project 
##### Repository for models trained on DocVQA

## Abstract
Systems that use natural language processing architectures, such as BERT, have proven to be very effective in the question answering task. However, the Document Visual Question Answering (DocVQA) challenge focuses on the visual question answering task, in which a visual understanding of the information in a document image is necessary to provide an answer. Therefore, in order to accurately recognize the text fields of interest and obtain a good performance in the task, it is inevitable to take advantage of the multimodal nature of the documents, where the textual, visual and layout information must be modeled together and learned end to end in a single structure. This article explores the effectiveness of the T5, LayoutLM and EffficientNet models applied to the context of Visual Question Answering and also explores the feasibility of architectures formed from the combination of these models in task 1 of the DocVQA challenge. The experiments showed that T5 only and T5+EfficientNet have a reasonable performance for the proposed task, reaching a F1 Score up to 60 points, while T5+LayoutLM presented learning difficulties during training.

## DocVQA dataset

DocVQA comprises 50,000 questions in 12,767 images. Data is randomly divided by an 80/10/10 ratio for training, validation and testing. The test dataset has 39, 463 questions and 10, 194 images, while the validation dataset has 5, 349 questions and 1, 286 images and the test contains 5, 188 questions and 1, 287 images.
The images provided in the dataset come from documents hosted at the Industry Documents Library, maintained by UCSF. The set of images was extracted from more than 6 thousand documents used in the industry, most of these, from a period between 1960-2000. There are documents from all the 5 main industries for which the library hosts documents, which are tobacco, food, drugs, fossil fuels and chemicals. The documents contain a mixture of printed, typewritten and handwritten content. A wide variety of document types are used for this task, including letters, memos, notes, reports, etc. The answers to the questions are short excerpts from the text. This means that the responses comprise a set of contiguous text tokens present in the document. There may be more than one valid answer per question. In this case, a list of possible correct answers is provided in the dataset.

![DocVQA](figs/docvqa_sample.JPG)
<br />

## DocVQA Challenge

The DocVQA challenge focuses on the task of Visual Question Answering (VQA). Distinct from conventional information extraction tasks, the VQA task is more complex and goes far beyond understanding the textual content of a document, but also visual and layout information including fonts, colors, as well as elements such as marks, checkboxes, separators, diagrams and other informations like page structure, forms or tables, which is vital for understanding the documents. The challenge has three different tasks. In task 1, natural language questions are defined in a single document and the answer needs to be given by interpreting the document's image. The Task 2 goal is to identify and recover all documents in a collection of documents that are relevant to answering this question, as well to provide an answer. Finally, Task 3 is a VQA task in which natural language questions are defined in single images, but this time the images are infographics on different topics where visual information is more relevant to answer the questions asked.
To this end, I propose three approaches to deal with task 1 of the DocVQA challenge, where each approach uses different architectures and input data. The first experiment consisted of using a T5 Base model and the textual information extracted by OCR as an input, which includes the question and the context of the document. For the second experiment two models were used, which are LayoutLM and T5 together with the textual features used in the previous step and the OCR 2D position information provided by the dataset as the inputs. For the third experiment, two models were also applied. This time EfficientNet and T5, with the visual features provided by the scanned document, plus the textual features from the OCR. The experiments illustrate the difficulties imposed by the challenge. Among the three architectures implemented, one had a hard time learning the task, while the other two had better results and outperformed the BERT Baseline model provided by the authors of the paper DocVQA.

## Models
### T5

In 2019, Raffel et al. introduced a new model called “Text-to-Text Transfer Transformer” or T5. This model presents an unified structure that converts all language tasks into a text-to-text framework, in which inputs and outputs are texts. This format provides a simple way to train and run inference on a single model on a wide variety of tasks, such as machine translation, document summarization, question-answering, and classification, using the same loss function and decoding procedure. The implementation of the T5 closely follows the Transformer model originally proposed by Vaswani et al. Composed of encoder and decoder, each of which consists of two sub-components: a self-attention layer, followed by a feed forward network. The model is pre-trained on the Colossal Clean Crawled Corpus (C4), which is a pre-processed version of a publicly available texts extracted from the web. The T5 model was provided in 5 different sizes (Base, Small, Large, 3B and 11B). The framework demonstrated state-of-the-art performance in several natural language processing tasks.

![T5](figs/t5.png)
<br />

### LayoutLM

Using BERT-Base as a backbone and inspired by its architecture, the authors of LayoutLM added two types of input embeddings, a 2-D positional embedding and an image embedding. These extra embeddings were incorporated into the architecture because the first one can capture the relationship between tokens within a document, while the second one captures some appearance features, such as fonts, types, colors and represents these image attributes in a language representation.\newline
LayoutLM was pre-trained on the IIT-CDIP Test Collection 1.0, which contains more than 6 million documents with 11 million document images in various categories. The authors used two tasks for pre-training:
* **Masked Visual-Language Model:** consists of, during pre-training, randomly masking some of the input tokens, but maintaining the corresponding 2-D position embeddings and then training the model to predict the masked tokens. In this way, the model, in addition to understanding the language context, also uses the corresponding 2-D position information. newline
* **Multi-label Document Classification:** Given a set of digitized documents, the authors used the document tags to supervise the pre-training process so that the model can group knowledge from different domains and generate a better representation at the document level. \newline

For the first time, the textual and image layout information of scanned documents were pre-trained in a single structure. This made the model obtain state-of-the-art performance in tasks of understanding images in documents and significantly surpassed several other text only pre-trained models.

![LayoutLM](figs/LayoutLM.JPG)
<br />

### EfficientNet

EfficientNeT is a model launched in 2019 by Google. The authors used Neural Architecture Search to build an efficient network architecture. The main building block of EfficientNet consists of MBConv, to which is added the compression and excitation optimization that form a shortcut connection between the beginning and the end of a convolutional block. Input activation maps are first expanded using 1x1 convolutions to increase the depth of resource maps. This is followed by 3x3 Depth-wise convolutions and Point-wise convolutions that reduce the number of channels on the output resource map. Shortcut connections connect the narrow layers, while the wider layers are present between the jump connections. This structure helps to decrease the overall number of operations required, as well as the size of the model. In order to improve accuracy and efficiency, the authors proposed a simple yet effective scaling technique, which uses a composite coefficient to uniformly dimension the width, depth and resolution of the network in a principled manner. In addition, all layers or stages in scale models use the same convolution operations as the baseline network called EfficientNet-b0. This technique allowed the authors to produce models that provided greater precision than existing convolutional networks, in addition to a monumental reduction in the number of FLOPS and the size of the model.

![Effcient](figs/EfficientNet.JPG)
<br />

## Experiments
### T5 only

The first experiment consisted of training two T5 models, the T5 model from Transformers library and a T5-Base pretrained on Natural Questions dataset. Both models received only textual information as input (question and document's context).

![t5](figs/T5.JPG)
<br />

### LayoutLM + T5 

For the second VQA experiment, I used an architecture composed of the LayoutLM and T5 models. The architecture receives as input the same textual information as the models of the previous experiment, together with the OCR position data provided by the dataset. All of these features are passed to LayoutLM, which creates an embedding representation, before moving on to T5, which is the model responsible for providing an answer to the question.

![layoutt5](figs/Layout+t5.JPG)
<br />

### EfficientNet + T5

At the third VQA experiment, I used an architecture composed of the EfficientNet and T5 models. The architecture receives as input the same textual information as the previous models, but this time it also receives the visual information from the scanned documents. In this implementation, visual embeddings go through CNN, while textual embeddings goes through the T5 encoder. At the end the two representations are concatenated and passed to the T5 decoder so that the model can provide an answer.

![efft5](figs/eff+t5.JPG)
<br />

## References
[1] Kwiatkowsk, T ., Palomaki , J., Redfield , O. (2019). Natural Questions: a Benchmark for Question Answering Research. Transactions of the Association of Computational Linguistics. <br />
[2] Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, L., and Polosukhin, I. (2017). Attention is all you need. CoRR, abs/1706.03762.<br />
[3] Devlin, Jacob, et al. (2018) "Bert: Pre-training of deep bidirectional transformers for language understanding." arXiv preprint arXiv:1810.04805.<br />
[4] C. Raffel, N. Shazeer, A. Roberts, K. Lee, S. Narang, M.Matena, Y. Zhou, W. Li and J. Liu. (2019) Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer.  <br />
