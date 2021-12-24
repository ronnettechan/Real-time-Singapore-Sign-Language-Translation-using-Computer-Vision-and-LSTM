# Singapore Sign Language Translation using Computer Vision and LSTM Model

## Problem Statement
Singapore Sign Language (SgSL) is used by the deaf community in Singapore and all over the world, Sign language has been developed to suit local contexes and has been used for many years. Unfortunately, Sign Language still faces a lack of recognition and awareness in many parts of the world and in Singapore too. There is also a general lack of interpreters for Sign Language. In order to bridge the gap in communication between users and non-users of Sign Language, promote awareness of Sign Language and the Deaf community, and encourage citizens to learn Sign Language, this project aims to develop a real-time SgSL translator using computer vision and neural networks (LSTM) where users can sign in front of a camera and the translation in english will appear on the screen.

## Background
Singapore Sign Language (SgSL) is the native sign language used in Singapore and was influenced by Shanghainese Sign Language, American Sign Language, Signing Exact English and other locally developed signs. SgSL is just one of the few ways that people with hearing loss communicate. Some may also use speech and listning to communicate, such as using lip-reading, or oral-based methods that amplifies the individual's residual hearing through assistive devices such as hearing aids or cochlear implants ([*source*](https://sadeaf.org.sg/about-us/about-sadeaf/)).

Sign Language has it's own grammar and linguistic structure that is different from that of spoken languages. Contrary to common belief, it is not a visual representation of spoken language and does not relate grammatically to any spoken language.
([*source*](https://sadeaf.org.sg/about-deafness/about-sign-language/)).
Hence this project was named 'Singapore Sign Language Translation' and not '..Interpretation' or '..Detection'.

Project outline:
- Collect keypoints from landmarks as data for training the model
    - Data was collected using local webcam
    - for each action, 30 videos were collected, each video containing 30 frames, each frame containing an array of landmarks detected in the frame.
- Data for general greeting actions and all 26 letters of the alphabet were collected, resulting in a total of 960 videos that formed the entire dataset.
- Stacked LSTM modelling was used as the detections were made in sequential order.
- The epoch loss and accuracy was tracked in real-time using Tensorboard
- The model was optimised by changing the number of layers, number of neurons in the layers, as well as batch size
- The final LSTM model had an accuracy of 0.83.

## Further Information
SgSL was recently adopted officially in the Deaf education in signing-designated mainstream schools in 2018 although it has existed in the deaf community way before that.
SgSL has 5 main parameters (similar to speech sounds in spoken language): handshape, orientation, location, movement, and non-manual markers (i.e. features that are beyond the hand such as facial expressions, mouthing, head/body movement). This project aims to cover 4 out of 5 of the parameters in SgSL - Handshape, Orientation, Location and Movement ([*source*](https://www.sgslsignbank.org.sg/sgsl/language-parameters)).

Signs used in the data collection and model training were taken from the SgSL Sign Bank as well as a deaf-led enterprise, ExtraOrdinary Horizons ([*source*](https://www.eohorizons.com/)).

## Keypoint collection
- Pose landmarks: (x, y, z, visibility) will always return landmarks but visibility value in each landmark may vary
- Left hand landmarks: (x, y, z) if no value is returned/error is returned -> nothing is detected
- Right hand landmarks: (x, y, z) if no value is returned/error is returned -> nothing is detected <br />
<br />
- pose (x, y, z, visibility) has 33 landmarks -> 33 * 4 values for each landmark
- left hand and right hand (x, y, z) have 21 landmarks -> 21 * 3 values for each landmark
- face (x, y, z) has 468 landmarks -> 468 * 3 values for each landmark

For data collection, each action will have 30 videos, each video is 30 frames in length and each frame collects. data will be saved using numpy.save (np.save). Each frame contains an array of values of landmarks detected.

Initially, only 20 sequences (videos) were collected for each Sign, however the model results were sub-par due to the lack of data. Hence the number of sequences was increased to 30.

Also, face landmarks was also initially collected. However, the model showed difficulties in differentiating between similar Signs and it was hypothesised that the face landmarks were contributing to the lack of differentiation. Mediapipe's Facemesh detects 468 landmarks on the face and this initially contributed to majority of each sequence collected. After removing the face landmarks from the data collection, the model performance improved and misclassification was greatly reduced.


## LSTM model
Although the original LSTM model contains a single hidden LSTM layer followed by a standard feedforward output layer, Stacked LSTMs were used in this project, which is an extension to the original LSTM model that has multiple hidden LSTM layers where each layer contains multiple memory cells.

Stacked LSTMs were used due to the sequential nature of the prediction. The previous LSTM layer provides a sequence output to the next LSTM layer.

Baseline model scores:
- Train Accuracy: 0.41
- Test Accuracy: 0.33
- Train loss: 1.77
- Test loss: 2.20

Final model scores:
- Train Accuracy: 0.99
- Test Accuracy: 0.83
- Train loss: 0.02
- Test loss: 0.60

Changes to hyperparameters:
- increased number of LSTM and dense layers
- increased number of neurons
- increased number of epochs
- attempted batch, stochastic and minibatch gradient descent

## Conclusion
The final model performance was a big improvement from the baseline model with an accuracy of 0.83. The model also performed relatively well during the real-time prediction.

Although it should be noted that even though the model performed well in the modelling stage, the deployment of the model for real-time prediction requires additional factors. Some of the factors that would affect prediction are:
- lighting:
In areas with insufficient or uneven lighting, the keypoint detection on the user's body would not be stable, hence good and even lighting is important.
- color and type of clothing:
It had been observed during some trials that if the color of clothing was close to the user's skin tone and if the user wore long baggy sleeves, the keypoint detection on the user's body would be unstable as well
- similar actions:
certain sign language signs appear similar to each other, which could result in wrong predictions sometimes. To counter this, during data collection, the differentiating movement or sign was done slower so that more sequences will be captured on the unique movements/actions.

## Further Recommendations
The project can be expanded to include more signs than just the basic alphabets and greetings that it is currently trained on. Furthermore, a reverse function in the translation can also be added such that people could type in words and the relevant signs will appear on the screen.
