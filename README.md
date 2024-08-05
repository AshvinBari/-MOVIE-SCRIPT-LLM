# MOVIE-SCRIPT-LLM
HOW CAN YOU CREATE A DATASET FROM A MOVIE SCRIPT ? 

[gpt-3.5-turbo-16k](https://community.openai.com/t/gpt-3-5-turbo-16k-maximum-response-length/266264)

 [EleutherAI/gpt-neo-2.7B](https://huggingface.co/EleutherAI/gpt-neo-2.7B)
 
# 1. Extract Dialogue from the Script
You need to extract lines spoken by Jules Winnfield and any relevant contextual information. This can be done manually or with the help of a script parser if the script format is consistent.

# 2. Manual Extraction or Script Parsing
# Manual Extraction:
Read through the script and manually copy lines spoken by Jules.
Note down the context manually.
# Script Parsing:
Write a script to parse the text file and identify dialogue lines based on character names and formatting cues.

# 3. Annotate Context and Swearing Level
After extracting the dialogues, annotate each line with context and swearing level.
Example Annotation Process:
Context: Briefly describe the situation or scene (e.g., "Interrogating someone").
Swearing Level: Assign a level based on the intensity of language (0 to 5).

# 4. Save the Annotated Dataset
Save the annotated dataset to a file format suitable for training, such as JSONL.

# Full Example Code
Here’s a complete example that combines extraction and annotation:
```python
import re
import json
import openai
from langchain.chat_models import ChatOpenAI

# Set your OpenAI API key
api = 'Enter_Your_Open_API_Key'

def extract_dialogue(script_text):
    dialogues = []
    current_speaker = 'JULES'
    current_context = None

    for line in script_text.split('\n'):
        # Detect speaker
        match = re.match(r'^\s*(\w+)\s*$', line)
        if match:
            current_speaker = match.group(1)
            continue

        # Detect context (manual or automated depending on the script format)
        if current_speaker == 'JULES':
            dialogues.append({
                'text': line.strip(),
                'context': current_context or 'Unknown'
            })

    return dialogues

def generate_context(text):
    try:
      chat=ChatOpenAI( model_name='gpt-3.5-turbo-16k',
            # temperature = self.config.llm.temperature,
            openai_api_key = 'Enter_Your_Open_API_Key',
            # max_tokens=self.config.llm.max_tokens
        )

      ans= chat.invoke(f"give one word context for this dialogue from Pulp fiction movie {text}")
      return ans

    except Exception as e:
        print(f"Error generating context: {e}")
        return "Unknown"

def annotate_dialogues(dialogues):
    annotated = []
    for dialogue in dialogues:
        text = dialogue['text']
        # Generate context using OpenAI API
        context = generate_context(text)
        # Determine swearing level
        swearing_level = 0
        if 'fuck' in text or 'motherfucker' in text:
            swearing_level = 5
        elif 'shit' in text or 'ass' in text:
            swearing_level = 3
        elif 'nigga' in text:
            swearing_level = 3  # Contextual, might adjust based on preference

        annotated.append({
            'text': text,
            'context': context,
            'swearing_level': swearing_level
        })

    return annotated

# Load script
with open('Jules_Vincent.json', 'r') as file:
    script_text = file.read()
# script_text = """
# JULES
# Oh, I'm sorry, did I break your concentration?
# JULES
# You were saying something about best intentions?
# JULES
# What's the matter?
# """

with open('jules_winnfield_dialogues.jsonl', 'w') as file:
    for dialogue in annotated_dialogues:
        # Extract the content from the AIMessage object
        dialogue['context'] = dialogue['context'].content  # Assuming 'context' holds the AIMessage
        file.write(json.dumps(dialogue) + '\n')

print("Dialogues have been extracted, annotated, and saved to jules_winnfield_dialogues.jsonl.")




