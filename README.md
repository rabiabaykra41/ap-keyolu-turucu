# ap-keyolu-turucu
!pip install groq together openai
import os
import time
from openai import OpenAI
from groq import Groq
import requests
from together import Together


# Set the API key for the OPENROUTER API
os.environ["GROQ_API_KEY"] = "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"  # Replace with your actual OpenAI key
# Set the API key for the NVIDIA API
nvidia_api_key = "OHVzMXBxYnBibGlhcDNzZWRta21qdDJtbzE6OTRmMjBiMjAtZGQ1Ni00YWIzLWIwM2EtYmNlNzk4Y2E3NTE4"
# Set the API key for OpenRouter API
os.environ["OPENAI_API_KEY"] = "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"
#os.environ["OPENAI_API_KEY"] = "sk-or-v1-d28f31a29084d0a4a0f0dd6471ed3088cee2ec994bc9daf141fe85fff5c9e0**"  # Replace with your actual OpenAI key
os.environ["TOGETHER_API_KEY"] = "f18373fc2854214d3ce99e7ce157bb53e152a61a25f1b720e6e02d7f8beb1a1b"



'''
GROQ API
'''
# Initialize the Groq client
groq_client = Groq(
    api_key=os.environ.get("GROQ_API_KEY"),
)

# Measure time for Groq API
start_time_groq = time.time()

# Create a chat completion for Groq
groq_completion = groq_client.chat.completions.create(
    messages=[
        {
            "role": "user",
            "content": "Write e-mail regex with python",
        }
    ],
    model="llama3-8b-8192",
)

# Print the result for Groq API
print(groq_completion.choices[0].message.content)

end_time_groq = time.time()
execution_time_groq = end_time_groq - start_time_groq


'''
NVIDIA API
'''

# NVIDIA API URL'sini belirtin
nvidia_api_url = "https://integrate.api.nvidia.com/v1/chat/completions"

# Zaman ölçümünü başlat
start_time_nvidia = time.time()

# API çağrısı için istek verilerini hazırlayın
data = {
    "model": "meta/llama-3.1-70b-instruct",
    "messages": [{"role": "user", "content": "Write python regex for e-mail."}],
    "temperature": 0.2,
    "max_tokens": 1024,
    "stream": False  # Stream desteği yoksa bunu False yapın
}

# İstek başlıklarını belirtin
headers = {
    "Authorization": f"Bearer {nvidia_api_key}",
    "Content-Type": "application/json"
}

# NVIDIA API'ye POST isteği gönder
response = requests.post(nvidia_api_url, headers=headers, json=data)

# Yanıtı kontrol et ve yazdır
if response.status_code == 200:
    response_data = response.json()
    if "choices" in response_data:
        for choice in response_data["choices"]:
            print(choice["message"]["content"])
else:
    print(f"Error: {response.status_code}, {response.text}")

# Zaman ölçümünü bitir
end_time_nvidia = time.time()
execution_time_nvidia = end_time_nvidia - start_time_nvidia

'''
OPENROUTER API
'''

# Initialize OpenAI client for OpenRouter API
openrouter_client = OpenAI(
    base_url="https://openrouter.ai/api/v1",
    api_key=os.environ["OPENAI_API_KEY"],
)

# Measure time for OpenRouter API
start_time_openrouter = time.time()

try:
    # Create a chat completion for OpenRouter
    completion_openrouter = openrouter_client.chat.completions.create(
        extra_headers={
            "HTTP-Referer": "YOUR_SITE_URL",  # Optional
            "X-Title": "YOUR_APP_NAME",        # Optional
        },
        model="meta-llama/llama-3.1-405b-instruct:free",
        messages=[{"role": "user", "content": "Write e-mail regex with python"}]
    )
    
    # Print the result for OpenRouter API
    print(completion_openrouter.choices[0].message.content)

except Exception as e:
    print(f"An error occurred: {e}")

end_time_openrouter = time.time()
execution_time_openrouter = end_time_openrouter - start_time_openrouter



# Initialize Together client
together_client = Together(api_key=os.environ.get("TOGETHER_API_KEY"))

# Measure time for Together API
start_time_together = time.time()
try:
    together_response = together_client.chat.completions.create(
        model="meta-llama/Meta-Llama-3.1-405B-Instruct-Turbo",
        messages=[{"role": "user", "content": "write regex email python"}],
        max_tokens=None,
        temperature=0.7,
        top_p=0.7,
        top_k=50,
        repetition_penalty=1,
        stop=["<|eot_id|>", "<|eom_id|>"],
        safety_model="meta-llama/Meta-Llama-Guard-3-8B"
    )
    print("Together Response:", together_response.choices[0].message.content)

except Exception as e:
    print(f"Together Error: {e}")

end_time_together = time.time()
execution_time_together = end_time_together - start_time_together

# Print execution times
print(f"\nGROQ API Execution Time: {execution_time_groq:.2f} seconds")
print(f"\nTogether API Execution Time: {execution_time_together:.2f} seconds")
print(f"\nOpenRouter API Execution Time: {execution_time_openrouter:.2f} seconds")
print(f"\nNVIDIA API Execution Time: {execution_time_nvidia:.2f} seconds")
