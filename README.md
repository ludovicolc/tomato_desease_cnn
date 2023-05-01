# Problem statement
Tomato leaf diseases can have a significant impact on the yield and quality of tomato crops. 
These diseases are caused by various pathogens such as bacteria, viruses, and fungi, 
that can greatly reduce the yield and quality of the crop, and early detection and control is important to minimize their impact.

## Project's summary

The dataset used for training and evaluating the model is from Kaggle.
The model was trained on 9 different types of leaf diseases (and 1 healthy leaf class), using transfer learning with a pre-trained ResNet50 model. 

Hyperparameters optimization was performed using the Optuna library resulting in a final **F1 score of 99%** (final model). 
|                                  | precision     | recall        | f1-score |  support      |
| ------------------               | ------------- | ------------- | -------- | ------------- |
| Bacterial_spot                   | 0.99          | 0.97          | 0.98     | 425           |
| Early_blight                     | 0.98          | 1.00          | 0.99     | 480           |
| Late_blight                      | 0.99          | 0.98          | 0.98     | 463           |
| Leaf_Mold                        | 0.99          | 1.00          | 0.99     | 470           |
| Septoria_leaf_spot               | 0.99          | 0.99          | 0.99     | 436           |
| Two-spotted_spider_mite          | 0.99          | 0.97          | 0.98     | 435           |
| Target_Spot                      | 0.98          | 0.98          | 0.98     | 457           |
| Yellow_Leaf_Curl_Virus           | 0.99          | 1.00          | 0.99     | 490           |
| Mosaic_virus                     | 0.99          | 1.00          | 0.99     | 448           |
| Healthy                          | 0.98          | 1.00          | 0.99     | 481           |


# Key decisions
* Normalization: Mean and standard deviation values provided by the PyTorch documentation were utilized for normalization, which are the same values that were used for training the original model.
* Training the model: the training process for the new model based on ResNet50 was performed on the last 2 bottlenecks of the fourth layer and the last layer (the classifier), taking advantage of the valuable parameters already learned by the original one when it was trained on the Imagenet dataset, which includes a variety of plant images.
* Objective: minimizing the loss
* Learning rates: while the last layer was changed to reinitialize its parameters, the 2 bottlenecks were trained with lowers learning rates to prevent altering too much the already acquired information.
* Overfitting: the use of data augmentation and visualizations was implemented to prevent overfitting, plus ResNet50 implements batch normalization. 
* Epochs: The training process was divided into two phases. In the first phase, 20 epochs were used to adjust the hyperparameters using Optuna. In the second phase, 30 epochs were performed with the optimal parameters obtained from the first phase.

![Immagine 2023-01-27 114436_aa](https://user-images.githubusercontent.com/105851039/215067687-872757d2-6f3d-4c93-ad55-e9d74cee796f.png)

As the figure above illustrates, after the 20th epoch, there is still some room for improvement, but after the 25th epoch, the loss begins to stabilize and overfitting is imminent.

## Final considerations
The final model has superior overall performance, but there is a discrepancy in the calibration of the different classes, which could impact decision-making post-classification. Specifically, the "healthy" class is more accurately calibrated in the intermediate model (with the best parameters and 20 epochs) than the final model. 

One could argue that having a better "healthy" class calibration is better because is preferable to predict a diseased plant, verify the leaf,
and find that it is healthy, rather than predict a healthy plant and later discover that it had a disease that has since progressed. Modifying
the class weights in the loss function may assist in achieving a different calibration that addresses specific issues.

(intermediate model healthy: precision 0.99 | recall 0.99 | f1 score 0.99)
![1](https://user-images.githubusercontent.com/105851039/235435087-cbdafd5a-d8f9-4d02-8039-3d343e651ac6.jpg)

(final model healthy: precision 0.98 | recall 1.00 | f1 score 0.99)
![2](https://user-images.githubusercontent.com/105851039/235435361-aa71a106-9a09-4aa1-8378-b0f4f2ed2184.jpg)


*The notebook and data are available, so if you wish to adjust the parameters and create a new model that is tailored to your specific problem, you have the resources to do so.*

*More detailed information on the parameters selection and model optimization can be found in the accompanying notebook.* 
