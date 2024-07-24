#  Anaconda Enviroments

Python is a nightmare, in order to make it bearable we have to create virtual enviroments. First you want to download Anaconda:

https://docs.anaconda.com/free/anaconda/install/windows/

Then, once this is completed we want to open the _Anaconda Prompt_. This should be accessible in your search bar. When we create a new project we want to create and manage a new enviroment. You want to check what version of Python is the most stable as later versions have a lot of issues. So far best version = 3.10.
```javascript

conda create --name {env-name} python //Create a new enviroment with newest python version
conda create --name {env-name} python=3.10 //Specify version name

```
Then you activate the enviroment: 

```javascript

conda activate {env-name} // activate env
conda deactivate // deactivate env

```
In order to install packages you want to find the correct installation command in the Anaconda website:

https://anaconda.org/anaconda/repo

__You have a much better chance of your enviroment working when deploying if you do this!__

Here are some other commands that help with managing enviroments:

```javascript

conda env list // Get a list of envs
conda env remove --name {env-name} //Remove an enviroment

conda env export > enviroment.yaml // Save the enviroment packages in YAML

conda list // Get a list of packages

```
In order to run jupyter lab in your enviroment you need to install: 

```javascript
conda install -c conda-forge jupyterlab
conda install -c conda-forge ipykernel 
python -m ipykernel install --user --name {env-name} --display-name "Python ({env-name})" //To activate env for Jupyter
jupyter lab //To run the program
```
## Tensorflow GPU enabled 

When installing tensorlow you also need to install two other packages cudnn adn cudatoolkit. See this like for details on what packages work together:

https://www.tensorflow.org/install/source#tested_build_configurations

I found this to be extremely difficult, alternatively pyTorch is very straight forward. 

## Pytorch GPU enabled

Here are my commnads to get pytorch working:

```bash 
conda create --name bayes-optimisation python=3.9
conda install pytorch torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia
```
The pytorch command was generated from their homepage 

https://pytorch.org/get-started/locally/


