# Data for the CMSSW [RecoHGCal/TICL](https://github.com/cms-sw/cmssw/tree/master/RecoHGCal/TICL) package

## Quicklinks

- TICL:
  - [Reconstruction example](http://hgcal.web.cern.ch/hgcal/Reconstruction/TICL/)
- TensorFlow:
  - [C++ docs](https://www.tensorflow.org/api_docs/cc)
  - [CMSSW interface](https://gitlab.cern.ch/mrieger/CMSSW-DNN)
  - [PhysicsTools/TensorFlow](https://github.com/cms-sw/cmssw/tree/master/PhysicsTools/TensorFlow)

## Models

- `tf_models/energy_id_v*.pb`: TensorFlow model for trackster energy regression and particle ID.
  - `v0`: Simple CNN-based approach. The neutral pion, neutral hadron, ambiguous and unknown probabilities are set to a constant value of 0. See the [talk at the Reco/AT meeting](https://indico.cern.ch/event/841640/contributions/3534140/attachments/1896780/3129591/2019-08-23_rieger_hgcal_ticl_eid.pdf) for more info. Input and output tensors:
    - `"input"`: Input tensor with dimension `batch x 50 (layers) x 10 (clusters) x 3 (features)`.
    - `"output/id_probabilities"`: Output tensor with dimension `batch x 8` representing particle ID "probabilities" (from a softmax output). The probabiltities refer to photon, electron, muon, neutral pion, charged hadron, neutral hadron, ambiguous and unknown cases (in that order).
    - `"output/regressed_energy"`: Output tensor with dimension `batch x 1` representing the regressed energy value for the trackster.

- `tf_models/energy_regression_without_pattern_recognition.pb`: TensorFlow model for trackster energy regression
  - This model tries to learn correction coefficients for different parts of the detector (e.g. CEE-120µm, CEH-Fine-300 µm, CEH-Coarse-Scintillators, etc.)
  - Each coefficient is implemented as a very small dense neural network with the energy sum of its category and the position (η) as input.
  - This network has been trained on layerClusters without pattern recognition. It is therefore expected that it wil perform poorly (response < 1) in any scenario after any pattern recognition has been applied. Better solutions are work in progress, so consider this as a temporary place holder to implement the functionality.
