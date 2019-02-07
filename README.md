# greenScreenKeying
Some tests using CNN to generate an alpha channel from images captured with a green screen, for a use in VFX / compositing of images

## What is compositing ?
Compositing is a part of the post production process in the film industry where you assemble various images into one. The sources of these images are mixed : some comes from "real footage" (captured by a camera), some others are generated by computers, using 3D techniques (using 3D softwares) or 2D techniques (using 2D softs like Photoshop). 

## What is Keying ?
Keying is the term usually used by the compositors to extract the foreground of images shot on a greenscreen, removing the green screen and replacing it by something else. They can do it by using specific softwares that can isolate the green color of the screen and make it "transparent" in order to replace it by other images.

## What's the point of using a convolutionnal neural net for Keying ?
Keying can be a tedious process for various reasons : first off, during the shooting, the greenscreen may not be lit correctly, or there may be problem of exposures during capture, making it difficult to isolate the green screen. On top of that, you can have "motion blur", or semi-transparent areas, or thin objects like hairs that are difficult to treat, compared to hard edges where the limit between the object and the screen is more obvious.
So compositors usually use various "keyers" for a same shot, and then have to manually "mix" those areas of transparencies. They work on the image "per zones".

==> Could a CNN perform faster than a human on keying if we provide him good examples ? COuld it reach a sufficient level of quality in this process ?

## How does it work ?
Images are given with a PNG format, easily readable by OpenCV (and probably Tensorflow, but I used OpenCV for knowing it better). Those images contain 3 channels (Red, Green and Blue), coded on 8 bit. I later convert them to float by dividing by 255, to get a value between 0 and 1.

I have used footage from the online training company FXPHD, because I could get those PNG but also have the "alpha channel", which is a 4th channel that contain the degree of transparency of a given pixel (same as above, I convert it to float to get a value between 0 and 1).

I have setup a basic Convolutionnal Neural Net to learn how to predict the transparency of each pixel :*

- Inputs : I read batches of 4 images Full HD (1920 x 1080 x 3 channels). I split them in 9 (3 x 3) using OpenCV, for memory purposes. I then recombine the output.

- first layer : convolution with a kernel size of 5 x 5 pixels, a stride of 1 x 1 (same resolution) and activation ReLu. That layer gives me 32 features map. So the output tensor is of shape (batch, img_size_W, img_size_H, 32)

- second layer : I do a maxpooling on the depth (channels), with a stride of 2. That gives me a tensor of shape (batch, img_size_W, img_size_H, 16)

- third layer : convolution layer of kernel size 1x1, stride 1x1 that produces 32 feature maps. Activation ReLu

- fourth layer : convolution layer of kernel size 3x3, stride 1x1 that produces 16 feature maps. Activation ReLu as well.

- fifth layer : convolution layer of kernel size 1x1 with output 1 feature map, and activation Sigmoid to get the transparency between 0 and 1.

The training has last 2 hours for 100 epochs. I will publish the results on a test set that the CNN has never seen at all during the training.

I already shared the trained netwrok if you want to test it.

ENVIRONMENT :
Windows 7
Python 3.6.8
Numpy 1.15.4
OpenCV 3.4.1
Tensorflow(-gpu) 1.12.0

