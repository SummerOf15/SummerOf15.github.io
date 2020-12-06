---
layout: _hight
title: Useful TensorFlow 2.x code segments
date: 2020-06-20 10:04:26
tags: python

---
## Useful TensorFlow 2.x code segments

### learning rate warm up

~~~python
args.start_lr=1e-6
args.learning_rate=5e-5
def myschedule(step,args,base_lr):
    if step <= 500:
        base_lr=args.start_lr+(args.learning_rate-args.start_lr)*step/float(500)
    else:
        if (step + 1) % (0.6 * args.iter_max) == 0:
            base_lr = base_lr / 5
        if (step + 1) % (0.8 * args.iter_max) == 0:
            base_lr = base_lr / 5
    return base_lr
~~~
<!-- more -->
### update learning rate in each epoch

~~~python
for epoch in range(args.iter_max):
        lr=myschedule(epoch,args,lr)
        optimizer = tf.keras.optimizers.Adam(learning_rate=lr)
        image_batch,label_batch = train_data.next_batch_finetune()
        with tf.GradientTape() as tape:
            y_hat = model(image_batch)
            r_loss=mse(y_hat, label_batch)
            loss = r_loss
        grads=tape.gradient(loss,model.variables)
        optimizer.apply_gradients(zip(grads,model.variables))
~~~

### print time stamp during training

~~~python
@tf.function
def printbar():
    ts = tf.timestamp()
    today_ts = ts%(24*60*60)

    hour = tf.cast(today_ts//3600+8,tf.int32)%tf.constant(24)
    minite = tf.cast((today_ts%3600)//60,tf.int32)
    second = tf.cast(tf.floor(today_ts%60),tf.int32)

    def timeformat(m):
        if tf.strings.length(tf.strings.format("{}",m))==1:
            return(tf.strings.format("0{}",m))
        else:
            return(tf.strings.format("{}",m))

    timestring = tf.strings.join([timeformat(hour),timeformat(minite),
                timeformat(second)],separator = ":")
    tf.print("=========="*8,end = "")
    tf.print(timestring)
~~~

### custom loss 

~~~ python
class Rank_loss():
    """Layer of Efficient Siamese loss function."""

    def __init__(self):
        self.margin = 6
        print('*********************** SETTING UP****************')
        pass

    @tf.function
    def get_rankloss(self, p_hat):
        loss=....
        return loss
    
rank_loss=Rank_loss()
loss=rank_loss.get_rankloss()
~~~

