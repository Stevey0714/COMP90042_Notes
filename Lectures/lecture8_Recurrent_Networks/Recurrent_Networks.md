# Lecture 8 Deep Learning for NLP: Recurrent Networks

<h2 id="rnn">Recurrent Networks</h2>

### Problem of N-gram Language Model

* Cen be implemented using counts with smoothing

* Can be implemented using feed-forward neural networks

* Problem: limited context

* E.g. Generate sentences using trigram model:
 > <img src="001.gif" alt="">

### Recurrent Neural Network(RNN)

* Allow representation of arbitrarily sized inputs

* Core idea: processes the input sequence one at a time, by applying a recurrence formula

* Uses a state vector to represent contexts that have been previously processed

* RNN Neuron:
    > <img src='002.png' alt="">
  
* RNN States:
    > <img src='003.png' alt="" width=420 height=275><br>
    > <img src="004.png" alt="" width=600 height=275><br>
    > Activation: <br><img src="005.png" alt="" width=800 height=275><br>
    
* RNN Unrolled:
    > <img src="006.png" alt="" width=750 height=360>
  
  * Same parameters <img src="https://render.githubusercontent.com/render/math?math=(W_s, W_x, b and W_y)" alt=""> are used across all time steps

* Training RNN:
    * An unrolled RNN is a very deep neural network. But parameters are shared across all time steps
    * To train RNN, just need to create the unrolled computation graph given an input sequence and use backpropagation algorithm to compute gradients as usual.
    * This procedure is called backpropagation through time. 
        > E.g of unrolled equation:<br>
        > <img src="007.png" alt=""><br>
      
### RNN Language Model:

<img src="008.png" alt="" width=700 height=330>

* <img src="https://render.githubusercontent.com/render/math?math=x_i" alt=""> is current word (e.g. `eats`) mapped to an embedding
  
* <img src="https://render.githubusercontent.com/render/math?math=s_{i-1}" alt=""> contains information of the previous words (e.g. `a` and `cow`)
  
* <img src="https://render.githubusercontent.com/render/math?math=y_i" alt=""> is the next word (e.g. `grass`)

* Training: 
    * Vocabulary: `[a, cow, eats, grass]`
    * Training example: `a cow eats grass`
    * Training process:
      > <img src="009.png" alt="" width=400 height=460><br>
      > <img src="https://render.githubusercontent.com/render/math?math=s_i = tanh(W_ss{i-1} %2B W_xx_i %2B b)" alt=""><br>
      > <img src="https://render.githubusercontent.com/render/math?math=y_i = softmax(W_ys_i)" alt="">
      
    * Losses:
        * <img src="https://render.githubusercontent.com/render/math?math=L_1 = -logP(0.30)" alt="">
          
        * <img src="https://render.githubusercontent.com/render/math?math=L_2 = -logP(0.50)" alt="">
          
        * <img src="https://render.githubusercontent.com/render/math?math=L_3 = -logP(0.20)" alt="">
          
        * Total loss: <img src="https://render.githubusercontent.com/render/math?math=L_{total} = L_1 %2B L_2 %2B L_3" alt="">
    
* Generation:
  > <img src="010.png" alt="" width=425 height=460>
  
* Problems of RNN:
    * Error Propagation: Unable to recover from errors in intermediate steps
    * Low diversity in generated language
    * Tend to generate bland or generic language
    
<h2 id="LSTM">Long Short-Term Memory Networks</h2>

### Long Short-Term Memory Model (LSTM)

* RNN has the capability to model infinite context. But it cannot capture long-range dependencies in practice due to the vanishing gradients

* Vanishing Gradient: Gradients in later steps diminish quickly during backpropagation. Earlier inputs do not get much update.

* LSTM is introduced to solve vanishing gradients

* Core idea: have memory cells that preserve gradients across time. Access to the memory cells is controlled by gates.

* Gates: For each input, a gate decides:
    * How much the new input should be written to the memory cell
    * How much content of the current memory cell should be forgotten
    
* Comparison between simple RNN and LSTM:
    > <img src="012.png" alt="" width=550 height=450>
    
### Gating Vector

* A gate <img src="https://render.githubusercontent.com/render/math?math=g" alt=""> is a vector. Each element of the gate has values between 0 and 1. Use sigmoid function to produce <img src="https://render.githubusercontent.com/render/math?math=g" alt="">.

* <img src="https://render.githubusercontent.com/render/math?math=g" alt=""> is multiplied component-wise with vector <img src="https://render.githubusercontent.com/render/math?math=v" alt=""> to determine how much information to keep for <img src="https://render.githubusercontent.com/render/math?math=v" alt="">
    > <img src="011.png" alt="" width=325 height=150>

### Forget Gate

<img src="013.png" alt="" width=600 height=200>
  
* Controls how much information to forget in the memory cell <img src="https://render.githubusercontent.com/render/math?math=C_{t-1}" alt="">

* E.g. Given `Tha cas that the boy` predict the next word `likes`
    * Memory cell was storing noun information `cats`
    * The cell should now forget `cats` and store `boy` to correctly predict the singular verb `likes`
    
### Input Gate

<img src="014.png" alt="" width=600 height=185>

* Input gate controls how much new information to put to memory cell

* <img src="https://render.githubusercontent.com/render/math?math=\widetilde{C}" alt=""> is new distilled information to be added

### Update Memory Cell

<img src="015.png" alt="" width=600 height=200>

* Use the forget and input gates to update memory cell

### Output Gate

<img src="016.png" alt="" width=550 height=190>

* Output gate controls how much to distill the content of the memory cell to create the next state

### Disadvantages of LSTM

* Introduces some but not many parameters
* Still unable to capture very long range dependencies
* Slower but not much slower than simple RNN

<h2 id="LSTM_application">Applications of RNN</h2>

### Example Applications

* Shakespeare Generator:
    * Training data: all works fo Shakespeare
    * Model: Character RNN, hidden dimension = 512
    
* Wikipedia Generator:
    * Training data: 100MB of Wikipedia raw data
    
* Code Generator

* Text Classification
    * RNNs can be used in variety NLP tasks. Particularly suited for tasks where order of words matter. E.g. sentiment analysis
    > <img src="017.png" width=590 height=230 alt="">
    
* Sequence Labeling: E.g. POS tagging
    > <img src="018.png" width=675 height=250 alt="">
  
### Variants of LSTM

* Peephole connections: allow gates to look at cell state
    > <img src="https://render.githubusercontent.com/render/math?math=f_t = \sigma(W_f[C_{t-1}, h_{t-1}, x_t] %2B b_f)" alt="">
  
* Gated recurrent unit (GRU): Simplified variant with only 2 gates and no memory cell
    > <img src="019.png" width=500 height=190 alt="">
  
* Multi-layer LSTM
    > <img src="020.png" width=600 height=420 alt="">
  
* Bidirectional LSTM
    > <img src="021.png" width=600 height=400 alt="">