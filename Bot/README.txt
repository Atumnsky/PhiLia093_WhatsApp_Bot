"""
PhiLia093 WhatsApp Bot

================================================================================
OVERVIEW
================================================================================
This is a friendly AI chatbot for WhatsApp groups, built using the `whatsapp-web.js` library. Named PhiLia093, the bot combines conversational AI with web search and expressive sticker reactions to create a warm, engaging group experience.

The bot uses:
- **whatsapp-web.js** – To interact with WhatsApp Web.
- **DeepSeek API** – For natural language understanding and generation.
- **Tavily Search API** – For real‑time web search capabilities.
- **Local sticker files** – To send expressive stickers in response to certain cues.

The bot is designed to be safe, respectful, and friendly, following a strict set of safety rules to avoid offensive language, inappropriate learning, or harmful interactions.

================================================================================
FEATURES
================================================================================
1. **Conversational AI** – Responds to messages starting with `#` in group chats. The bot uses a warm, gentle personality and may occasionally add a musical note (♪) at the end of sentences.

2. **Web Search** – Use `#search <query>` to fetch real‑time information via Tavily. The bot will incorporate search results into its answer and provide source URLs at the end.

3. **Auto‑Search for Time Queries** – If a message contains time‑related keywords (e.g., "what time", "current time") but does not start with `#search`, the bot automatically enables search to give an accurate answer.

4. **Sticker Reactions** – The AI can suggest sending a sticker by including `[STICKER: emotion]` in its reply (e.g., `[STICKER: happy]`). The bot then sends a random sticker from the corresponding folder after the text message. Available emotions depend on the stickers placed in the `./stickers` directory.

5. **Per‑User Conversation Memory** – The bot remembers the last ~1500 tokens of conversation per user (in a group, per participant) to maintain context.

6. **Cooldown** – Prevents spam by enforcing a 5‑second cooldown between messages from the same user.

7. **Creator Special Treatment** – When the creator (ID `195035186348201@lid`) speaks, the bot adds an extra system prompt to be extra warm and personal.

8. **Safety & Content Filtering** – The system prompt includes strict rules against offensive language, learning bad words, using made‑up words, or discussing sensitive topics like LGBTQ+. The bot is programmed to politely decline such requests.

9. **Online/Offline Announcements** – When the bot starts, it sends an "Online!" message to a configured group (if `ONLINE_GROUP_ID` is set). When it shuts down gracefully (Ctrl+C), it sends an "Offline!" message.

10. **Group List on Startup** – The bot logs all groups it is a member of to help confirm IDs.

================================================================================
COMMANDS
================================================================================
All commands must be sent in a group chat and start with `#`:

- `# <your message>` – Normal chat with PhiLia093.
- `#search <query>` – Perform a web search and get an answer with sources.
- (Auto‑search is triggered for time queries even without `#search`.)

The bot does **not** respond to messages that do not start with `#`.

================================================================================
CONFIGURATION (Environment Variables)
================================================================================
- `ONLINE_GROUP_ID` – The WhatsApp group ID where online/offline messages are sent. If not set, no announcement is made.
- `DEEPSEEK_API_KEY` – Your DeepSeek API key (required).
- `TAVILY_API_KEY` – Your Tavily search API key (required for search functionality).

Additionally, the bot hard‑codes:
- `CREATOR_ID` – The WhatsApp ID of the creator (`195035186348201@lid`) for special treatment. This can be changed.

Stickers must be placed in the `./stickers` folder, with subfolders named after emotions (e.g., `happy`, `sad`, `wave`). Inside each emotion folder, place image files (`.webp`, `.png`, `.gif`, `.jpg`). The bot will randomly pick one when that emotion is requested.

================================================================================
CODE STRUCTURE
================================================================================
- **Imports** – Required libraries.
- **Configuration** – API keys, IDs, constants.
- **Client Initialization** – Creates a WhatsApp client with `LocalAuth` for persistent session.
- **Memory & Cooldown** – `memory` object stores conversation history per user; `cooldown` tracks timestamps.
- **System Prompt** – `SYSTEM_PROMPT` defines personality, safety rules, and behavior.
- **Helper Functions**:
  - `trimMemory(messages, maxTokens)` – Limits conversation history to approximate token count.
  - `loadStickers()` – Scans `./stickers` folder and builds a map of emotion → file paths.
  - `sendSticker(chatId, emotion)` – Sends a random sticker for the given emotion.
  - `searchTavily(query, maxResults)` – Calls Tavily API and returns results.
- **Event Handlers**:
  - `client.on('qr')` – Displays QR code for authentication.
  - `client.on('ready')` – Logs groups and sends online message.
  - `client.on('message')` – Main message handler.
- **Graceful Shutdown** – `handleExit()` sends offline message and destroys client.
- **Client Initialization** – Starts the bot.

================================================================================
EXTENDING / MODIFYING THE BOT
================================================================================
- **Changing Personality or Rules**: Edit `SYSTEM_PROMPT`. Adjust tone, safety rules, or the sticker‑suggestion logic.
- **Adding New Commands**: Modify the message handler to detect new prefixes (e.g., `#weather`) and implement custom logic.
- **Search Behavior**: Change `maxResults` in `searchTavily()` or alter the auto‑search keywords list.
- **Sticker System**: Add new emotion folders in `./stickers`. Ensure the emotion name matches what the AI might output (case‑insensitive). You can also modify `sendSticker` to use different file types or sending logic.
- **Memory Management**: Adjust `trimMemory` token limit or the maximum history length (currently 30 messages per user after trimming).
- **Cooldown**: Change the cooldown period (currently 5000 ms) in the message handler.
- **Creator ID**: Update `CREATOR_ID` to another WhatsApp ID if desired.
- **Time Zone**: Modify the `timeZone` in the `currentTimeStr` generation to match your local time.
- **Error Handling**: Enhance try/catch blocks for more robust recovery.

================================================================================
NOTES & LIMITATIONS
================================================================================
- The bot only responds in **group chats** (messages from `@g.us`). It ignores private chats.
- The bot uses `whatsapp-web.js`, which requires a working WhatsApp Web session. The first time you run it, you must scan the QR code with your WhatsApp mobile app. The session is saved via `LocalAuth` for subsequent runs.
- Stickers must be in supported formats (`.webp` works best for stickers). The bot sends them as stickers (using `sendMediaAsSticker: true`).
- The AI may occasionally suggest a sticker; you can control how often by adjusting the system prompt.
- The bot does **not** have a persistent knowledge base like the Discord version; all memory is in‑memory and lost on restart.
- The auto‑search feature is triggered by simple keyword matching; it may not be perfect.
- Ensure your Tavily API key has sufficient quota; the bot calls it for every `#search` and auto‑detected time query.
- The DeepSeek API may have rate limits or costs; monitor usage accordingly.
- The bot filters messages starting with `#` only; it will ignore other messages to avoid cluttering the chat.

================================================================================
AUTHOR & LICENSE
================================================================================
This bot was created for the WhatsApp community. It is open for modification and redistribution under the terms of the MIT License (if applicable). Feel free to adapt it to your needs.

For questions or contributions, please contact the original developer.

Enjoy chatting with PhiLia093! ✨
"""