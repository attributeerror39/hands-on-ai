# Prompting

## The Default Workflow

This is the Default  Workflow for generating images from a single checkpoint file.
![Default](images/default.png)


### Negative Prompting

Instead of using prompts to get images we want to see, we can use the same textutal descriptions to exlude concepts from our generation process.

![Negative Prompting](images/negative_prompting.png)

### Prompt Weighting

Using the special syntax (concept:weight) for example (car:1.5) we can exaggerate or diminish the influence of concepts.

![Prompt Weighting](images/prompt_weights.png)

### Combining Embeddings

We can also traverse the possibility space by using multiple embeddings (called conditioning in ComfyUI). Depending on the concepts used this will merge the concepts if possible.

![Combining Embeddings](images/combining_embeddings.png)

### Combining Image Concepts

Similar to combining multiple textual concepts we can use the add-on *ipadapter* to combine visual concepts aswell.

![IP Adapter](images/ipadapter.png)
