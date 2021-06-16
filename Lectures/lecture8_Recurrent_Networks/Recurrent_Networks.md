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
    > <img src="004.png" alt="" width=350 height=275><br>
    > Activation: <img src="550.png" alt="" width=420 height=275><br>
    
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