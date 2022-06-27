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
  - DEPRECATED
  - This model tries to learn correction coefficients for different parts of the detector (e.g. CEE-120µm, CEH-Fine-300 µm, CEH-Coarse-Scintillators, etc.)
  - Each coefficient is implemented as a very small dense neural network with the energy sum of its category and the position (η) as input.
  - This network has been trained on layerClusters without pattern recognition. It is therefore expected that it wil perform poorly (response < 1) in any scenario after any pattern recognition has been applied. Better solutions are work in progress, so consider this as a temporary place holder to implement the functionality.

- `tf_models/energy_regression_after_pattern_recognition.pb`: TensorFlow model for trackster energy regression
  - for functionality: see above
  - this model is trained on hadronic single particle events (Klong) 
  - Input: sum of all energies of different detector parts in an event **after** pattern recognition 
  - Ouput: Regressed energy assuming all energy deposits originate from a single particle
  - IMPORTANT: This is not trained on individual tracksters (Work in progress) and since it is by design not linear in the 
    energy input it will give different (i.e. worse) results if a particle is split into multiple tracksters. 

- `energy_regression_tracksters.pb`: Tensorflow model trained on tracksters
  - Big difference: This model is trained to predict the energy correctly on trackster level, not on event level
  - For inference there is no fundamental change
  - The expected input variable are now `$f_0, ..., f9, \eta, E_{raw}, p_0, ..., p1$` where `$f_i = E_i / E_{raw}` and `$p_i$` are the ID probabilities
