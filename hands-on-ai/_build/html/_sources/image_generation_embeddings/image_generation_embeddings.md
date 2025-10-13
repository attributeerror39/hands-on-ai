# Image generation & Embeddings

## Quick-reference: Key Terms in Diffusion Models

- **Image**  
  A 2-D grid of colour numbers; the finished picture we want the model to output.

- **Pixel Space**  
  The huge coordinate system where each dimension corresponds to one pixel value. Diffusion starts by adding noise here and ends by turning noise back into an image.

- **Latent Space**  
  A compact, learned space (fewer, more abstract channels) where the model does most of its thinking. Nearby points describe visually similar concepts.

- **Vector (Embedding)**  
  One point in latent space—just an ordered list of numbers that encodes a concept, an image patch, or a text prompt.

- **Model**  
  The trained neural network (usually a U-Net with attention) that learns how to gradually remove noise and map latent vectors back to pixel space.

- **Checkpoint**  
  A saved snapshot of the model’s weights at a particular moment in training. Loading a checkpoint lets you resume training or generate images with that exact skill-set.

- **Prompt**  
  The text (or other conditioning input) fed to the model that tells it *what* to draw. The prompt is turned into vectors and guides the denoising steps toward a matching image.

