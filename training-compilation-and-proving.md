# ✨ Training, Compilation and Proving

## One example to rule them all

Here is a minimal example of how to train a model that can be used to prove a statement:

```python
"""
Demonstrates how to train a simple model on the MNIST dataset and
check the constraints of the model using the BionettaModel class. After
the training, we save the model locally.
"""

import numpy as np
import tensorflow as tf
import tf_bionetta as tfb
from tf_bionetta.specs.backend_enums import ProvingBackend, WitnessGenerator
from tf_bionetta.specs.target import TargetPlatform


# 1. Load the MNIST dataset first
(X_train, y_train), (X_test, y_test) = tf.keras.datasets.mnist.load_data()
X_train = X_train / 255.0
X_test = X_test / 255.0
X_train = np.expand_dims(X_train, axis=-1)  # Add channel dimension
X_test = np.expand_dims(X_test, axis=-1)  # Add channel dimension
y_train = tf.keras.utils.to_categorical(y_train, 10) # One-hot encode the labels
y_test = tf.keras.utils.to_categorical(y_test, 10) # One-hot encode the labels

# 2. Define the model architecture
model = tf.keras.models.Sequential([
    tf.keras.layers.Input(shape=(28,28,1)),
    tf.keras.layers.Flatten(input_shape=(28,28,1)),
    tf.keras.layers.Dense(64),
    tfb.layers.ShiftReLU(5), # LeakyReLU with alpha=1/(2**5)=1/32
    tf.keras.layers.Dense(10)
], name='simple_mnist_model')

# 3. Wrap the model architecture with our custom BionettaModel class
model = tfb.BionettaModel(model, verbose=2)
model.constraints_summary()
test_input = X_test[np.random.randint(len(X_test))]

# 4. Compile and train the model
model.compile(loss='mse', optimizer='adam')
model.fit(X_train, y_train, 
    epochs=1, 
    validation_data=(X_test, y_test), 
    validation_split=0.2
)

# 5. Compile the model
model.compile_circuits(
    path='./examples/simple_mnist_circuits',
    test_input=test_input,
    save_weights=True,
    proving_backend=ProvingBackend.GROTH16,
    target_platform=TargetPlatform.DESKTOP,
    witness_backend=WitnessGenerator.CUSTOM
)

# 6. Init your architecture from compiled dir
loaded_model = tfb.BionettaModel.load_from_compiled_folder('./examples/simple_mnist_circuits', verbose=2)
test_input = X_test[np.random.randint(len(X_test))]

# 7. Prove and verify your input. Simple MNIST should be previously compiled!!!
proof_dir = './examples/simple_mnist_circuits/proof'

proof = loaded_model.prove(
    input=test_input,
    target_dir=proof_dir
)

print('The resulted proof:', proof)

assert loaded_model.verify(proof_dir=proof_dir), "model verification failed"
```

