# music generation readme
## Summary
The purpose of this program is to generate monophonic MIDI files leveraging Long Short-Term Memory Recurring Neural Network (LSTM RNN) using existing MIDI files.
### Encoding MIDIs
To encode the MIDIs, we used a matrix of N rows with 128 columns with each row representing a beat in the song. When a note i is played with beat j, we set (j, i) = 1, any other elements are set to 0. The resulting matrix is sparsely populated.
### Forming Training Data
The music generation is constructed when given the previous K notes, the next note is predicted. A window size must be chosen to form the rolling windows of k consecutive notes (rows from the matrix), and the note (row) that follows. The k notes will be the input features (X), and the preceding note will be the predicted value (y).
### Long Short-Term Memory
LSTMs help preserve the error that can be backpropagated through time and layers. By maintaining a more constant error, they allow recurrent nets to continue to learn over many time steps (over 1000), thereby opening a channel to link causes and effects remotely.

LSTMs capture and contain information outside of the flow of the recurrent network inside of a gated cell through an input gate. The information is then stored in, written to, or read from the cell, which is a memorizing and forgetting process. The cell will make decisions of what to store, and when reading, writing, and erasing can be performed.

The gates take the signals they receive and apply weights which are adjusted by the recurrent networks to determine whether they block or pass information. Essentially, the cells are learning when to allow data to enter, leave, or be deleted through an iterative process of guessing, backpropagating, and readjusting the weight with gradient descent.
### Pretty Midi
pretty_midi contains utility function/classes for handling MIDI data, so that it’s in a format from which it is easy to modify and extract information.
### Scraping Tool
We created a midi scraping tool to extract MIDI files from http://www.piano-e-competition.com/midi_2018.asp

## Setup
To start, we ran the music-generation model by using a virtual machine (GPU+) in Paperspace with tensor-flow preconfigured.
Our repository is saved in GitHub @
https://github.com/JavierPalomares90/music-generation/tree/feature/training-pretty-midi

To clone the Github file, from the terminal, run:
```
git clone https://github.com/JavierPalomares90/music-generation.git
```
To checkout the git Branch for pretty-midi, run:
```
git checkout feature/training-pretty-midi
```

Move the midi files from midi_files/reformated to data/midi

Now that we have gathered the midi files that we obtained from using the midi_scraper.py, we will train our data by running:
```
python3 train.py --data_dir data/midi
```

After training the data, use the following command to generate a unique MIDI file
```
python3 generate.py
```
10 MIDI files are created by default using the newest training checkpoint in experiments/ with files generated in generated/. The model can be specified using –experiment_dir


## Command Line Arguments
### train.py
*	--data_dir: A folder containing .mid (or .midi) files to use for training. All files in this folder will be used for training.
*	--experiment_dir: The name of the folder to use when saving the model checkpoints and Tensorboard logs. If omitted, a new folder will be created with an auto-incremented number inside of experiments/.
*	--rnn_size (default: 64): The number of neurons in hidden layers.
*	--num_layers (default: 1): The number of hidden layers.
*	--learning_rate (default: the recommended value for your optimizer): The learning rate to use with the optimizer. It is recommended to adjust this value in multiples of 10.
*	--window_size (default: 20): The number of previous notes (and rests) to use as input to the network at each step (measured in 16th notes). It is helpful to think of this as the fixed width of a piano roll rather than individual events.
*	--batch_size (default: 32): The number of samples to pass through the network before updating weights.
*	--num_epochs (default: 10): The number of epochs before completing training. One epoch is equal to one full pass through all midi files in --data_dir. Because of the way files are lazy loaded, this number can only be an estimate.
*	--dropout (default: 0.2): The normalized percentage (0-1) of weights to randomly turn "off" in each layer during a training step. This is a regularization technique called which helps prevent model overfitting. Recommended values are between 0.2 and 0.5, or 20% and 50%.
*	--optimizer (default: "adam"): The optimization algorithm to use when minimizing your loss function. See https://keras.io/optimizers for a list of supported optimizers and and links to their descriptions.
*	--grad_clip (default: 5.0): Clip backpropagated gradients to this value.
### generate.py

*	--experiment_dir (default: most recent folder in experiments/): Directory from which to load model checkpoints. If left unspecified, it loads the model from the most recently added folder in experiments/.
*	--save_dir (default: generated/ inside of --experiment_dir): Directory to save generated files to.
*	--midi_instrument (default: "Acoustic Grand Piano"): The name (or program number, 0-127) of the General MIDI instrument to use for the generated files. A complete list of General MIDI instruments can be found here.
*	--num_files (default: 10): The number of MIDI files to generate.
*	--file_length (default: 1000): The length of each generated MIDI file, specified in 16th notes.
*	--prime_file: The path to a .mid file to use to prime/seed the generated files. A random window of this file will be used to seed each generated file.
*	--data_dir: Used to select random files to prime/seed from if --prime_file is not specified.

## Research
Colin Raffel and Daniel P. W. Ellis. Intuitive Analysis, Creation and Manipulation of MIDI Data with pretty_midi. In 15th International Conference on Music Information Retrieval Late Breaking and Demo Papers, 2014.

https://skymind.ai/wiki/lstm

