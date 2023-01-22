## TensorFlow Serving

>Wat TensorFlow Serving is en hoe we denken/dachten dat we het konden gebruiken.
>Wat de moeilijkheden daarbij waren.


In de Python documentatie van Tensorflow is een passage genaamd [`TPUClusterResolver`](https://docs.w3cub.com/tensorflow~2.4/distribute/cluster_resolver/tpuclusterresolver.html). Dat klinkt interessant! De parameter `tpu` stelt het volgende voor: "A string corresponding to the TPU to use. It can be the TPU name or TPU worker gRPC address."

[gRPC](https://grpc.io/) klinkt als iets wat we nodig hebben!

> gRPC is a modern open source high performance Remote Procedure Call (RPC) framework that can run in any environment. It can efficiently connect services in and across data centers with pluggable support for load balancing, tracing, health checking and authentication. It is also applicable in last mile of distributed computing to connect devices, mobile applications and browsers to backend services.

Binnen Tensorflow bestaat er dus een [API](https://github.com/tensorflow/tensorflow/tree/v2.4.0/tensorflow/python/distribute#tensorflow-distribute-libraries) die het mogelijk maakt om distributed training toe te passen over meerder GPU's of TPU's. Ook bestaande modellen kunnen op deze manier draaien.

Onder "Distributed training with TensorFlow" is er een [`tf.distribute.TPUStrategy`](https://www.tensorflow.org/api_docs/python/tf/distribute/TPUStrategy) object. Dit object neemt als eerste parameter een `tpu_cluster_resolver` . Dat klinkt al veelbelovend. Wat is een [`tpu_cluster_resolver`](https://www.tensorflow.org/api_docs/python/tf/distribute/cluster_resolver/TPUClusterResolver)?

` tf.distribute.cluster_resolver.TPUClusterResolver` neemt als eerste parameter `tpu`, dat kan wederom een gRPC adres zijn of een lokaal apparaat. 

Hopelijk kan er met de [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) een cluster van Edge TPU's samengesteld worden om te gebruiken in een distributed TPU Strategy. 

In de eerder omschreven documentatie wordt gRPC een aantal keer vermeld. Zo een gRPC server is wat Google Cloud onder andere gebruikt om zijn Edge TPU's toegankelijk te maken over het internet. Er zal dus een gRPC server moeten opgezet worden op de Dev Board nodes.

Dit kan door gebruik te maken van [Tensorflow Serving](https://github.com/tensorflow/serving). Dat is een server en AP die dat hopelijk kan gebruikt worden om op iedere node te installeren. TF Serve ondersteunt gRPC en kan het zo mogelijk maken om de TPU's aan elkaar te linken in een Python of C++ script.

Het is niet gelukt om TF Serving te installeren op een van de Edge TPU's omdat de [binaries](http://storage.googleapis.com/tensorflow-serving-apt) niet gecompileerd zijn voor ARM. Er was niet meer genoeg tijd om de software zelf te compileren.

Een python script om meerdere Edge TPU's samen te laten werken zou er ongeveer als volgt uit kunnen zien:

```python
# https://github.com/tensorflow/tensorflow/tree/v2.4.0/tensorflow/python/distribute#custom-training-loop-with-tpus

import tensorflow as tf

# https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec
cluster = tf.train.ClusterSpec({"worker": ["192.168.88.12:50051",
                                           "192.168.88.13:50051",
                                           "192.168.88.14:50051"],
                                "ps": ["192.168.88.11:2222",
                                       "ps1.example.com:2222"]})

# https://www.tensorflow.org/guide/distributed_training
cluster_resolver = tf.distribute.cluster_resolver.TPUClusterResolver(
    cluster)
tf.config.experimental_connect_to_cluster(cluster_resolver)
tf.tpu.experimental.initialize_tpu_system(cluster_resolver)

# Create the strategy instance.
tpu_strategy = tf.distribute.TPUStrategy(resolver)


# Create the keras model under strategy.scope()
with tpu_strategy.scope():
  model = keras.layers.Dense(1, name="dense")

# Create custom training loop body as tf.function.
@tf.function
def train_step(iterator):
  def step_fn(inputs):
    images, targets = inputs
    with tf.GradientTape() as tape:
      outputs = model(images)
      loss = tf.reduce_sum(outputs - targets)
    grads = tape.gradient(loss, model.variables)
    return grads

  return tpu_strategy.run(
      step_fn, args=(next(iterator),))

# Run the loop body once on at dataset.
dataset = tf.data.Dataset.from_tensors(([1.], [1.])).repeat(100).batch(10
input_iterator = iter(tpu_strategy.experimental_distribute_dataset(dataset))
train_step(input_iterator)
```

Bovenstaande code is echter nog niet getest.



## Bronnen

> 1. https://www.tensorflow.org/guide/distributed_training
> 2. https://www.tensorflow.org/api_docs/python/tf/distribute/TPUStrategy
> 3. https://www.tensorflow.org/api_docs/python/tf/distribute/cluster_resolver/TPUClusterResolver
> 4. https://github.com/tensorflow/serving
> 5. http://storage.googleapis.com/tensorflow-serving-apt
