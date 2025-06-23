# 📃 Standardized Circuit Inputs Specifications

## Generic Inputs Format

Below is an example of a ZK Circuit configuration:

```json
{
  "ultra_groth": "1",
  "address": "1",
  "threshold": "107374",
  "nonce": "1",
  "features": [
    "-508", "-172", "-164", "-561", "39451",
    "1598", "2776", "-3756", "2598", "-988"
  ],
  "image": [],
  "rand": "1"
}
```

**Parameter Breakdown**

* **`ultra_groth`**:\
  Indicates whether the UltraGroth16 proving system is used.
  * `"1"` means enabled.
* **`address`**:\
  An arbitrary identifier. Often used to bind the proof to a specific target, such as a contract address or user context.
* **`threshold`**:\
  A numeric threshold value used for checking whether the folded output of the neural model exceeds a certain decision boundary.
* **`nonce`**:\
  An arbitrary value, added to the proof to detect and prevent proof reuse during verification.
* **`features`**:\
  An array of numerical values representing the initial feature vector input to the model. These are the core values used for comparison or classification in the ZK circuit.
* **`image`**:\
  (Optional) An array to hold image data passed to the model, such as pixel values or serialized tensors. Can be empty if not used.
* **`rand`**:\
  A random value, added to the proving process to ensure additional entropy and improve ZK security.
