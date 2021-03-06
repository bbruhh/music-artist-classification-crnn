# Musical Artist Classification with Convolutional Recurrent Neural Networks

Z. Nasrullah, Y. Zhao, Musical Artist Classification with Convolutional Recurrent Neural Networks. *International Joint Conference on Neural Networks (IJCNN)*, 2019. **Submitted, under review**.

## Introduction
Previous attempts at music artist classification use frame-level audio features which summarize frequency content within short intervals of time. Comparatively, more recent music information retrieval tasks take advantage of temporal structure in audio spectrograms using deep convolutional and recurrent models. This paper revisits artist classification with this new framework and empirically explores the impacts of incorporating temporal structure in the feature representation. To this end, an established classification architecture, a Convolutional Recurrent Neural Network (CRNN), is applied to the \textit{artist20} music artist identification dataset under a comprehensive set of conditions. These include audio clip length, which is a novel contribution in this work, and previously identified considerations such as dataset split and feature-level. Our results improve upon baseline works, verify the influence of the production details on classification performance and demonstrate the trade-offs between sample length and training set size. The best performing model achieves an average F1-score of 0.937 across three independent trials which is a substantial improvement over the corresponding baseline under similar conditions. Finally, to showcase the effectiveness of the CRNN's feature extraction capabilities, we visualize audio samples at its bottleneck layer demonstrating that learned representations segment into clusters belonging to their respective artists.


![Convolutional Recurrent Neural Network](https://github.com/ZainNasrullah/musical-artist-classification/blob/final/images/crnn_arch.png)


## Dependency
The experiment code are writen in Python 3.6 and built on a number of Python packages including (but not limited to):
- Keras==2.1.1
- Tensorflow==1.10.0
- librosa==0.5.1
- numpy==1.15.0
- pandas==0.23.4
- scipy==1.1.0
- scikit_learn==0.20.0


Batch installation is possible using the supplied "requirements.txt" with pip or conda.

````cmd
pip install -r requirements.txt
````

## Datasets

This study primarily uses the artist20 musical artist identification dataset by Labrosa [1]. The data is accessible upon request from https://labrosa.ee.columbia.edu/projects/artistid/.

The main characteristics of the dataset can be summarized as:

|Property | Value |
|---------| ------|
|# of Tracks| 1,413|
|# of Artists| 20|
|Albums per Artist| 6|
|Bitrate| 32 kbps|
|Sample Rate| 16 kHz|
|Channels| Mono|

The figure below visualizes three seconds of the mel-scaled audio spectrogram for a randomly sampled song from each artist. This is the primary data representation used in the paper. 

![Convolutional Recurrent Neural Network](https://github.com/ZainNasrullah/musical-artist-classification/blob/final/images/artists.PNG)

## Usage

To re-create experimental results:

- Prepare mel-scaled spectrograms from raw audio in the dataset.
    - Run src/utility.py if the dataset is stored using its original folder structure (artists/[artist]/[album]/[song].mp3) in the project root.
    - Using the create_dataset() utility function in src/utility.py with a custom directory if the dataset is stored elsewhere.
- Run the main.py script. This will begin a training loop which runs three independent trials for each audio length in {1s, 3s, 5s, 10s, 20s, 30s}.
    - This script must be adjusted manually to vary whether or not to use an album split via the album_split flag in the train_model function call. 
    - It should be noted that training each model is computationally expensive and can take several hours even with reliable hardware. At minimum, a Nvidia GTX 1080 GPU is recommended with at least 16GB of memory on the machine.  
- To reproduce the representation visualization, the representation.py script can be used but one must specify the model weight location and relevant audio clip length. 

The models and utility functions provided can also generically be used for any audio-based classification task where one wants to experiment with audio length. The train_model function in src/trainer.py is fairly extensive. 

## Results

Classification performance is evaluated using the test F1-score of three independent trials and also varying parameters such as audio length {1s, 3s, 5s, 10s, 20s, 30s}, the type of dataset split {song-level, album-level} and feature-level {frame-level, song-level}. Both the average and maximum score are reported among the trials. 

As a whole, from the four base conditions resulting from audio split and level, the CRNN model outperforms the most comparable baseline for at least one audio clip length. This holds true for both the best and average case performance except for the album split with song-level features where the CRNN model only outperforms in its best-run. This discrepancy may be explained by considering that Mandel's dataset contains less classes or because, unlike the baselines works, we are additionally reporting the average of three independent trials instead of performance on a single trial. 

*Test F1 Scores for Frame-level Audio Features (3 runs):*

|Split | Type | 1s | 3s | 5s | 10s | 20s | 30s| 
|---|---|---|---|---|---|---|---|
|Song | Average | 0.729 | 0.765 | 0.770 | **0.787** | 0.768 | 0.764|
|Song | Best | 0.733 | 0.768 | 0.779 | 0.772 | **0.792** | 0.771|
|Album | Average | 0.482 | 0.513 | 0.536 | 0.538 | 0.534 | **0.603**|
|Album | Best | 0.516  | 0.527 | 0.550 | 0.560 | 0.553 | **0.612**|

*Test F1 Scores for Song-level Audio Features (3 runs):*

|Split | Type | 1s | 3s | 5s | 10s | 20s | 30s| 
|---|---|---|---|---|---|---|---|
|Song | Average | 0.929 | **0.937** | 0.918 | 0.902 | 0.861 | 0.846|
|Song | Best | 0.944 | **0.966** | 0.930 | 0.915 | 0.880 | 0.851|
|Album | Average | 0.641 | 0.651 | 0.652 | 0.630 | 0.568 | **0.674**|
|Album | Best | **0.700**  | 0.653 | 0.662 | 0.683 | 0.609 | 0.691|

Additionally, audio samples at the bottleneck layer of the network are also visualized using t-SNE to demonstrate how effectively the model is able to learn to classify artists. As can be seen below, the learned representations prior to classification separate into distinct clusters belonging to each artist demonstrating that the convolution and recurrent layers are effective at the task. The example below is for the model trained on 10s of audio.  

![Learned representations at bottleneck layer of network (10s)](https://github.com/ZainNasrullah/musical-artist-classification/blob/final/images/representation_313.png)

## Conclusions
This paper establishes a deep learning baseline for music artist classification on the \textbf{\textit{artist20}} dataset and demonstrates that a Convolutional Recurrent Neural Network is able to outperform traditional baselines under a range of conditions. Our experiments demonstrate that additional temporal structure initially improves performance, but then starts diminishing beyond the ten second mark. This is attributed to a lack of complexity in the model or to early pooling layers discarding too much information. Using the trained models, predictions are also aggregated at the song-level using a majority vote to determine the artist performing a song. This led to another substantial gain in performance and validates the viability of using this type of model for industry applications such as copyright detection. The best-performing model is trained using three seconds audio samples under a song-split and evaluated at the song-level to achieve an impressive average F1-score of 0.937 across three independent trials. Finally, we visualize audio samples at the bottleneck layer of the network to show that learned representations cluster by artist, before the classification layer in the model, highlighting the model's capability as a feature extractor. Future directions should aim to explore data augmentation, pre-training and methods to minimize temporal pooling; it is expected that this will lead to another substantial performance improvement. 

## Citations

[1] D. Ellis (2007). Classifying Music Audio with Timbral and Chroma Features,
Proc. Int. Conf. on Music Information Retrieval ISMIR-07, Vienna, Austria, Sep. 2007.
