FROM python:3.13-slim

WORKDIR /app

RUN apt update && apt install -y git && \
    git clone --depth 1 https://github.com/Echoshard/Gemini_Discordbot .

RUN sed -i 's/^PyMuPDF.*/PyMuPDF==1.24.14/' requirements.txt

RUN pip install --no-cache-dir -r requirements.txt

CMD ["python", "GeminiDiscordBot.py"]
