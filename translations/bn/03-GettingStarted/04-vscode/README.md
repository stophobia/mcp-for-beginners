<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "54e9ffc5dba01afcb8880a9949fd1881",
  "translation_date": "2025-07-04T16:31:17+00:00",
  "source_file": "03-GettingStarted/04-vscode/README.md",
  "language_code": "bn"
}
-->
চলুন পরবর্তী অংশে ভিজ্যুয়াল ইন্টারফেস কীভাবে ব্যবহার করা হয় তা নিয়ে আরও আলোচনা করি।

## পদ্ধতি

উচ্চ স্তরে আমাদের কীভাবে এগোতে হবে তা এখানে দেওয়া হলো:

- আমাদের MCP সার্ভার খুঁজে পেতে একটি ফাইল কনফিগার করা।
- উক্ত সার্ভারটি চালু/সংযোগ করা যাতে এটি তার ক্ষমতাগুলি তালিকাভুক্ত করতে পারে।
- GitHub Copilot Chat ইন্টারফেসের মাধ্যমে উক্ত ক্ষমতাগুলি ব্যবহার করা।

দারুণ, এখন যেহেতু আমরা প্রবাহটি বুঝে গেছি, চলুন একটি অনুশীলনের মাধ্যমে Visual Studio Code ব্যবহার করে MCP সার্ভার ব্যবহার করার চেষ্টা করি।

## অনুশীলন: একটি সার্ভার ব্যবহার করা

এই অনুশীলনে, আমরা Visual Studio Code কনফিগার করব যাতে এটি আপনার MCP সার্ভার খুঁজে পায় এবং GitHub Copilot Chat ইন্টারফেস থেকে এটি ব্যবহার করা যায়।

### -0- প্রাকধাপ, MCP সার্ভার আবিষ্কার সক্ষম করা

আপনাকে MCP সার্ভার আবিষ্কার সক্ষম করতে হতে পারে।

1. Visual Studio Code-এ যান `File -> Preferences -> Settings`।

2. "MCP" অনুসন্ধান করুন এবং settings.json ফাইলে `chat.mcp.discovery.enabled` সক্ষম করুন।

### -1- কনফিগ ফাইল তৈরি করা

আপনার প্রকল্পের মূল ফোল্ডারে একটি কনফিগ ফাইল তৈরি করুন, যার নাম হবে MCP.json এবং এটি .vscode নামক ফোল্ডারে রাখতে হবে। এটি দেখতে এরকম হওয়া উচিত:

```text
.vscode
|-- mcp.json
```

এখন, চলুন দেখি কীভাবে একটি সার্ভার এন্ট্রি যোগ করা যায়।

### -2- একটি সার্ভার কনফিগার করা

*mcp.json* ফাইলে নিম্নলিখিত বিষয়বস্তু যোগ করুন:

```json
{
    "inputs": [],
    "servers": {
       "hello-mcp": {
           "command": "node",
           "args": [
               "build/index.js"
           ]
       }
    }
}
```

উপরের উদাহরণে একটি Node.js-এ লেখা সার্ভার শুরু করার সহজ উপায় দেখানো হয়েছে, অন্য রানটাইমের জন্য `command` এবং `args` ব্যবহার করে সার্ভার শুরু করার সঠিক কমান্ড উল্লেখ করুন।

### -3- সার্ভার শুরু করা

এখন যেহেতু আপনি একটি এন্ট্রি যোগ করেছেন, চলুন সার্ভার শুরু করি:

1. *mcp.json* এ আপনার এন্ট্রি খুঁজে বের করুন এবং নিশ্চিত করুন যে আপনি "play" আইকনটি দেখতে পাচ্ছেন:

  ![Visual Studio Code-এ সার্ভার শুরু করা](../../../../translated_images/vscode-start-server.8e3c986612e3555de47e5b1e37b2f3020457eeb6a206568570fd74a17e3796ad.bn.png)  

2. "play" আইকনে ক্লিক করুন, GitHub Copilot Chat-এ টুলস আইকনের পাশে উপলব্ধ টুলসের সংখ্যা বাড়তে দেখবেন। যদি আপনি ওই টুলস আইকনে ক্লিক করেন, তাহলে নিবন্ধিত টুলগুলির একটি তালিকা দেখতে পাবেন। আপনি প্রতিটি টুল চেক/আনচেক করতে পারেন, নির্ভর করে আপনি GitHub Copilot কে সেগুলো প্রসঙ্গ হিসেবে ব্যবহার করতে চান কিনা:

  ![Visual Studio Code-এ টুলস আইকন](../../../../translated_images/vscode-tool.0b3bbea2fb7d8c26ddf573cad15ef654e55302a323267d8ee6bd742fe7df7fed.bn.png)

3. একটি টুল চালাতে, এমন একটি প্রম্পট টাইপ করুন যা আপনার টুলগুলির বর্ণনার সাথে মেলে, উদাহরণস্বরূপ "add 22 to 1" এর মতো একটি প্রম্পট:

  ![GitHub Copilot থেকে একটি টুল চালানো](../../../../translated_images/vscode-agent.d5a0e0b897331060518fe3f13907677ef52b879db98c64d68a38338608f3751e.bn.png)

  আপনি ২৩ ফলাফল হিসেবে দেখতে পাবেন।

## অ্যাসাইনমেন্ট

আপনার *mcp.json* ফাইলে একটি সার্ভার এন্ট্রি যোগ করার চেষ্টা করুন এবং নিশ্চিত করুন যে আপনি সার্ভার শুরু/বন্ধ করতে পারেন। এছাড়াও নিশ্চিত করুন যে আপনি GitHub Copilot Chat ইন্টারফেসের মাধ্যমে আপনার সার্ভারের টুলগুলোর সাথে যোগাযোগ করতে পারেন।

## সমাধান

[সমাধান](./solution/README.md)

## মূল বিষয়সমূহ

এই অধ্যায় থেকে মূল বিষয়গুলো হলো:

- Visual Studio Code একটি চমৎকার ক্লায়েন্ট যা আপনাকে একাধিক MCP সার্ভার এবং তাদের টুলস ব্যবহার করতে দেয়।
- GitHub Copilot Chat ইন্টারফেস হল সার্ভারগুলোর সাথে আপনার যোগাযোগের মাধ্যম।
- আপনি ব্যবহারকারীর কাছ থেকে API কী-এর মতো ইনপুট চাইতে পারেন যা *mcp.json* ফাইলে সার্ভার এন্ট্রি কনফিগার করার সময় MCP সার্ভারে পাঠানো যেতে পারে।

## নমুনা

- [Java Calculator](../samples/java/calculator/README.md)
- [.Net Calculator](../../../../03-GettingStarted/samples/csharp)
- [JavaScript Calculator](../samples/javascript/README.md)
- [TypeScript Calculator](../samples/typescript/README.md)
- [Python Calculator](../../../../03-GettingStarted/samples/python)

## অতিরিক্ত সম্পদ

- [Visual Studio ডকুমেন্টেশন](https://code.visualstudio.com/docs/copilot/chat/mcp-servers)

## পরবর্তী ধাপ

- পরবর্তী: [SSE সার্ভার তৈরি করা](../05-sse-server/README.md)

**অস্বীকৃতি**:  
এই নথিটি AI অনুবাদ সেবা [Co-op Translator](https://github.com/Azure/co-op-translator) ব্যবহার করে অনূদিত হয়েছে। আমরা যথাসাধ্য সঠিকতার চেষ্টা করি, তবে স্বয়ংক্রিয় অনুবাদে ত্রুটি বা অসঙ্গতি থাকতে পারে। মূল নথিটি তার নিজস্ব ভাষায়ই কর্তৃত্বপূর্ণ উৎস হিসেবে বিবেচিত হওয়া উচিত। গুরুত্বপূর্ণ তথ্যের জন্য পেশাদার মানব অনুবাদ গ্রহণ করার পরামর্শ দেওয়া হয়। এই অনুবাদের ব্যবহারে সৃষ্ট কোনো ভুল বোঝাবুঝি বা ভুল ব্যাখ্যার জন্য আমরা দায়ী নই।