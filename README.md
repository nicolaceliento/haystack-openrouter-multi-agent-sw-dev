
Per il corretto funzionamento del notebook jupyter sono necessarie le seguenti librerie: 
    
    openai haystack haystack-ai python-dotenv pyyaml ipywidgets

che possono essere instalalte mediante il comando:
    
    python -m pip install openai haystack haystack-ai python-dotenv pyyaml ipywidgets

Inoltre, è necessario creare un file vars.env con le seguenty keys:
    
    OPENAI_API_BASE=https://openrouter.ai/api/v1 #url openrouter API
    OPENAI_API_KEY=sk-openrouter-api-key da creare su https://openrouter.ai/settings/keys