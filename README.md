# RT Neural Network — Live Training Visualizer

A **single, self-contained `index.html`** that trains a neural network to recognize
hand-drawn digits **in real time**, right in your browser. No build step, no servers,
no external ML libraries — the entire network and backpropagation are implemented
**from scratch in vanilla JavaScript**.

> Open `index.html` in any modern browser and press **Space** to start training.

![status](https://img.shields.io/badge/dependencies-none-success) ![status](https://img.shields.io/badge/ML%20library-none%20(from%20scratch)-blueviolet)

---

## ✨ What you get

- **A neural network built from zero.** Fully-connected feedforward net
  (784 → hidden → 10 softmax), forward pass + **manual backpropagation**,
  mini-batch gradient descent, He/Xavier initialization, weights in `Float32Array`s.
  Three optimizers you can switch live: **SGD**, **Momentum**, and **Adam**.
- **A living network diagram.** Every layer is drawn on a canvas. The input is the
  28×28 grid; hidden/output neurons are circles. Connections are colored by weight
  **sign** (blue = positive, red = negative) and faded by **magnitude**, updating
  live as the net learns. Neuron brightness = current activation.
- **"Watch a prediction".** Pick a test sample and watch the forward pass pulse
  through the layers, lighting up activations, with the predicted class highlighted
  green (correct) or red (wrong).
- **Live charts, drawn by hand on canvas** (no chart libraries): a scrolling
  cross-entropy **loss curve** and **train/test accuracy** curves.
- **Confusion matrix** (10×10, color = count) and **per-class accuracy bars**,
  recomputed on the test set as training proceeds.
- **An interactive drawing pad** — the killer feature. Draw a digit with mouse or
  finger and the trained network classifies it **live as you draw**, showing animated
  probability bars and feeding your drawing through the visualized network.

---

## 🧠 The neural network (no libraries)

| Aspect            | Implementation                                                        |
|-------------------|-----------------------------------------------------------------------|
| Architecture      | `784 → [hidden…] → 10`, hidden layers configurable in the UI          |
| Hidden activation | ReLU                                                                   |
| Output            | Softmax                                                                |
| Loss              | Cross-entropy                                                          |
| Backprop          | Manual: forward pass caches activations, backward pass computes grads |
| Optimizers        | SGD · Momentum (μ=0.9) · Adam (β₁=0.9, β₂=0.999) with bias correction  |
| Init              | He for ReLU layers, Xavier for the softmax layer                      |
| Storage           | `Float32Array` weights/biases for speed                               |

For softmax + cross-entropy the output gradient simplifies cleanly to
`δ = a − one_hot(label)`, and the code is commented step by step so the math is
easy to follow.

## 🔢 The dataset (generated on the fly)

Embedding real MNIST as base64 is impractical to produce here, so the app uses the
**procedural-glyph approach** described in the brief: it renders the digits **0–9**
to an offscreen 28×28 canvas with **random rotation, scale, translation, font
variety, stroke jitter and pixel noise**. This creates a genuinely learnable,
MNIST-style dataset with **zero downloads** — it works fully offline.

- Configurable samples per class (40 / 80 / 120), split into **train** and **test**
  sets (~80/20), with accuracy reported on both.

## ⚡ Performance

Training runs **N mini-batches per `requestAnimationFrame`** (the *Speed* slider),
so the UI stays responsive — you can keep typing and drawing while the network
trains, and the visualization stays smooth.

---

## 🎮 Controls

| Action            | Button / Key                          |
|-------------------|---------------------------------------|
| Train / Pause     | **Space** or the ▶ button             |
| Step one batch    | **S** or the ⏭ button                 |
| Reset (reinit)    | **R** or the ↺ button                 |

**Live hyperparameters** (apply immediately): learning rate, batch size, optimizer,
speed (batches/frame). **Architecture** (number/size of hidden layers) and **dataset
size** require a **Reset** to apply — the UI shows a hint when a reset is needed.

## 🚀 Run it

```bash
# just open the file — no install, no build
open index.html        # macOS
xdg-open index.html    # Linux
```

Or double-click `index.html`. That's it.

---

## 🏗️ Code structure

The single file is organized into clearly labeled sections (comments in Slovak,
identifiers in English):

1. **NN core** — `Layer` / `Network` classes: forward, backward, optimizers.
2. **Dataset** — procedural digit rendering + train/test split.
3. **State & training loop** — mini-batch scheduling, evaluation.
4. **Visualization** — network diagram + live activations.
5. **Charts** — loss/accuracy line charts, confusion matrix, per-class bars.
6. **Drawing pad** — capture, downsample to 28×28, live classification.
7. **Sample viewer** + forward-pass animation.
8. **UI** — controls, stats, keyboard shortcuts, render loop.
