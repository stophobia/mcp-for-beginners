<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "3eaf38ffe0638867045ec6664908333c",
  "translation_date": "2025-06-18T09:21:09+00:00",
  "source_file": "03-GettingStarted/06-http-streaming/README.md",
  "language_code": "id"
}
-->
# HTTPS Streaming dengan Model Context Protocol (MCP)

Bab ini memberikan panduan lengkap untuk mengimplementasikan streaming yang aman, skalabel, dan real-time dengan Model Context Protocol (MCP) menggunakan HTTPS. Materi meliputi motivasi streaming, mekanisme transport yang tersedia, cara mengimplementasikan HTTP yang dapat distreaming dalam MCP, praktik keamanan terbaik, migrasi dari SSE, dan panduan praktis untuk membangun aplikasi streaming MCP Anda sendiri.

## Mekanisme Transport dan Streaming dalam MCP

Bagian ini membahas berbagai mekanisme transport yang tersedia di MCP dan perannya dalam memungkinkan kemampuan streaming untuk komunikasi real-time antara klien dan server.

### Apa itu Mekanisme Transport?

Mekanisme transport mendefinisikan bagaimana data dipertukarkan antara klien dan server. MCP mendukung beberapa jenis transport untuk menyesuaikan dengan berbagai lingkungan dan kebutuhan:

- **stdio**: Input/output standar, cocok untuk alat lokal dan berbasis CLI. Sederhana tapi tidak cocok untuk web atau cloud.
- **SSE (Server-Sent Events)**: Memungkinkan server mendorong pembaruan real-time ke klien melalui HTTP. Bagus untuk UI web, tapi terbatas dalam skalabilitas dan fleksibilitas.
- **Streamable HTTP**: Transport streaming berbasis HTTP modern, mendukung notifikasi dan skalabilitas yang lebih baik. Direkomendasikan untuk sebagian besar skenario produksi dan cloud.

### Tabel Perbandingan

Lihat tabel perbandingan berikut untuk memahami perbedaan antara mekanisme transport ini:

| Transport         | Pembaruan Real-time | Streaming | Skalabilitas | Kasus Penggunaan        |
|-------------------|--------------------|-----------|--------------|------------------------|
| stdio             | Tidak              | Tidak     | Rendah       | Alat CLI lokal         |
| SSE               | Ya                 | Ya        | Sedang       | Web, pembaruan real-time|
| Streamable HTTP   | Ya                 | Ya        | Tinggi       | Cloud, multi-klien     |

> **Tip:** Memilih transport yang tepat memengaruhi performa, skalabilitas, dan pengalaman pengguna. **Streamable HTTP** direkomendasikan untuk aplikasi modern yang skalabel dan siap cloud.

Perhatikan transport stdio dan SSE yang sudah Anda lihat di bab sebelumnya dan bagaimana streamable HTTP adalah transport yang dibahas dalam bab ini.

## Streaming: Konsep dan Motivasi

Memahami konsep dasar dan motivasi di balik streaming sangat penting untuk mengimplementasikan sistem komunikasi real-time yang efektif.

**Streaming** adalah teknik dalam pemrograman jaringan yang memungkinkan data dikirim dan diterima dalam potongan kecil yang mudah diatur atau sebagai rangkaian peristiwa, bukan menunggu seluruh respons siap. Ini sangat berguna untuk:

- File atau dataset besar.
- Pembaruan real-time (misalnya, chat, progress bar).
- Komputasi jangka panjang di mana Anda ingin terus memberi tahu pengguna.

Berikut hal penting yang perlu Anda ketahui tentang streaming secara umum:

- Data dikirim secara bertahap, tidak sekaligus.
- Klien dapat memproses data saat tiba.
- Mengurangi latensi yang dirasakan dan meningkatkan pengalaman pengguna.

### Mengapa menggunakan streaming?

Alasan menggunakan streaming adalah sebagai berikut:

- Pengguna mendapatkan umpan balik segera, bukan hanya di akhir.
- Memungkinkan aplikasi real-time dan UI yang responsif.
- Penggunaan sumber daya jaringan dan komputasi lebih efisien.

### Contoh Sederhana: Server & Klien HTTP Streaming

Berikut contoh sederhana bagaimana streaming dapat diimplementasikan:

<details>
<summary>Python</summary>

**Server (Python, menggunakan FastAPI dan StreamingResponse):**
<details>
<summary>Python</summary>

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
import time

app = FastAPI()

async def event_stream():
    for i in range(1, 6):
        yield f"data: Message {i}\n\n"
        time.sleep(1)

@app.get("/stream")
def stream():
    return StreamingResponse(event_stream(), media_type="text/event-stream")
```

</details>

**Klien (Python, menggunakan requests):**
<details>
<summary>Python</summary>

```python
import requests

with requests.get("http://localhost:8000/stream", stream=True) as r:
    for line in r.iter_lines():
        if line:
            print(line.decode())
```

</details>

Contoh ini menunjukkan server mengirimkan serangkaian pesan ke klien saat pesan tersebut tersedia, bukan menunggu semua pesan siap.

**Cara kerjanya:**
- Server mengirimkan setiap pesan saat sudah siap.
- Klien menerima dan mencetak setiap potongan data saat tiba.

**Persyaratan:**
- Server harus menggunakan respons streaming (misalnya, `StreamingResponse` in FastAPI).
- The client must process the response as a stream (`stream=True` in requests).
- Content-Type is usually `text/event-stream` or `application/octet-stream`.

</details>

<details>
<summary>Java</summary>

**Server (Java, menggunakan Spring Boot dan Server-Sent Events):**

```java
@RestController
public class CalculatorController {

    @GetMapping(value = "/calculate", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<ServerSentEvent<String>> calculate(@RequestParam double a,
                                                   @RequestParam double b,
                                                   @RequestParam String op) {
        
        double result;
        switch (op) {
            case "add": result = a + b; break;
            case "sub": result = a - b; break;
            case "mul": result = a * b; break;
            case "div": result = b != 0 ? a / b : Double.NaN; break;
            default: result = Double.NaN;
        }

        return Flux.<ServerSentEvent<String>>just(
                    ServerSentEvent.<String>builder()
                        .event("info")
                        .data("Calculating: " + a + " " + op + " " + b)
                        .build(),
                    ServerSentEvent.<String>builder()
                        .event("result")
                        .data(String.valueOf(result))
                        .build()
                )
                .delayElements(Duration.ofSeconds(1));
    }
}
```

**Klien (Java, menggunakan Spring WebFlux WebClient):**

```java
@SpringBootApplication
public class CalculatorClientApplication implements CommandLineRunner {

    private final WebClient client = WebClient.builder()
            .baseUrl("http://localhost:8080")
            .build();

    @Override
    public void run(String... args) {
        client.get()
                .uri(uriBuilder -> uriBuilder
                        .path("/calculate")
                        .queryParam("a", 7)
                        .queryParam("b", 5)
                        .queryParam("op", "mul")
                        .build())
                .accept(MediaType.TEXT_EVENT_STREAM)
                .retrieve()
                .bodyToFlux(String.class)
                .doOnNext(System.out::println)
                .blockLast();
    }
}
```

**Catatan Implementasi Java:**
- Menggunakan stack reaktif Spring Boot dengan `Flux` for streaming
- `ServerSentEvent` provides structured event streaming with event types
- `WebClient` with `bodyToFlux()` enables reactive streaming consumption
- `delayElements()` simulates processing time between events
- Events can have types (`info`, `result`) for better client handling

</details>

### Comparison: Classic Streaming vs MCP Streaming

The differences between how streaming works in a "classical" manner versus how it works in MCP can be depicted like so:

| Feature                | Classic HTTP Streaming         | MCP Streaming (Notifications)      |
|------------------------|-------------------------------|-------------------------------------|
| Main response          | Chunked                       | Single, at end                      |
| Progress updates       | Sent as data chunks           | Sent as notifications               |
| Client requirements    | Must process stream           | Must implement message handler      |
| Use case               | Large files, AI token streams | Progress, logs, real-time feedback  |

### Key Differences Observed

Additionally, here are some key differences:

- **Communication Pattern:**
   - Classic HTTP streaming: Uses simple chunked transfer encoding to send data in chunks
   - MCP streaming: Uses a structured notification system with JSON-RPC protocol

- **Message Format:**
   - Classic HTTP: Plain text chunks with newlines
   - MCP: Structured LoggingMessageNotification objects with metadata

- **Client Implementation:**
   - Classic HTTP: Simple client that processes streaming responses
   - MCP: More sophisticated client with a message handler to process different types of messages

- **Progress Updates:**
   - Classic HTTP: The progress is part of the main response stream
   - MCP: Progress is sent via separate notification messages while the main response comes at the end

### Recommendations

There are some things we recommend when it comes to choosing between implementing classical streaming (as an endpoint we showed you above using `/stream`) dibandingkan memilih streaming melalui MCP.

- **Untuk kebutuhan streaming sederhana:** Streaming HTTP klasik lebih mudah diimplementasikan dan cukup untuk kebutuhan streaming dasar.

- **Untuk aplikasi kompleks dan interaktif:** Streaming MCP memberikan pendekatan yang lebih terstruktur dengan metadata yang lebih kaya dan pemisahan antara notifikasi dan hasil akhir.

- **Untuk aplikasi AI:** Sistem notifikasi MCP sangat berguna untuk tugas AI yang berjalan lama dimana Anda ingin terus memberi tahu pengguna tentang kemajuan.

## Streaming dalam MCP

Baik, Anda sudah melihat beberapa rekomendasi dan perbandingan sejauh ini tentang perbedaan antara streaming klasik dan streaming dalam MCP. Mari kita bahas secara detail bagaimana Anda bisa memanfaatkan streaming dalam MCP.

Memahami bagaimana streaming bekerja dalam kerangka MCP sangat penting untuk membangun aplikasi responsif yang memberikan umpan balik real-time kepada pengguna selama operasi yang berjalan lama.

Dalam MCP, streaming bukan tentang mengirim respons utama secara bertahap, melainkan mengirim **notifikasi** ke klien saat sebuah tool memproses permintaan. Notifikasi ini bisa berupa pembaruan kemajuan, log, atau peristiwa lainnya.

### Cara kerjanya

Hasil utama tetap dikirim sebagai satu respons tunggal. Namun, notifikasi dapat dikirim sebagai pesan terpisah selama pemrosesan dan dengan demikian memperbarui klien secara real time. Klien harus dapat menangani dan menampilkan notifikasi ini.

## Apa itu Notifikasi?

Kita menyebut "Notifikasi", apa maksudnya dalam konteks MCP?

Notifikasi adalah pesan yang dikirim dari server ke klien untuk memberi tahu tentang kemajuan, status, atau peristiwa lain selama operasi yang berjalan lama. Notifikasi meningkatkan transparansi dan pengalaman pengguna.

Misalnya, klien diharapkan mengirim notifikasi setelah handshake awal dengan server selesai.

Notifikasi terlihat seperti pesan JSON berikut:

```json
{
  jsonrpc: "2.0";
  method: string;
  params?: {
    [key: string]: unknown;
  };
}
```

Notifikasi termasuk dalam topik di MCP yang disebut ["Logging"](https://modelcontextprotocol.io/specification/draft/server/utilities/logging).

Untuk mengaktifkan logging, server perlu mengaktifkannya sebagai fitur/kemampuan seperti berikut:

```json
{
  "capabilities": {
    "logging": {}
  }
}
```

> [!NOTE]
> Tergantung SDK yang digunakan, logging mungkin sudah aktif secara default, atau Anda mungkin perlu mengaktifkannya secara eksplisit dalam konfigurasi server Anda.

Ada berbagai jenis notifikasi:

| Level     | Deskripsi                      | Contoh Kasus Penggunaan        |
|-----------|-------------------------------|-------------------------------|
| debug     | Informasi debugging detail     | Titik masuk/keluar fungsi      |
| info      | Pesan informasi umum           | Pembaruan kemajuan operasi     |
| notice    | Peristiwa normal tapi penting  | Perubahan konfigurasi          |
| warning   | Kondisi peringatan             | Penggunaan fitur yang deprecated|
| error     | Kondisi kesalahan             | Kegagalan operasi              |
| critical  | Kondisi kritis                 | Kegagalan komponen sistem      |
| alert     | Tindakan harus segera diambil | Terdeteksi korupsi data        |
| emergency | Sistem tidak dapat digunakan   | Kegagalan sistem total         |

## Mengimplementasikan Notifikasi dalam MCP

Untuk mengimplementasikan notifikasi dalam MCP, Anda perlu menyiapkan sisi server dan klien agar dapat menangani pembaruan real-time. Ini memungkinkan aplikasi Anda memberikan umpan balik langsung kepada pengguna selama operasi yang berjalan lama.

### Sisi Server: Mengirim Notifikasi

Mari mulai dengan sisi server. Dalam MCP, Anda mendefinisikan tool yang dapat mengirim notifikasi saat memproses permintaan. Server menggunakan objek konteks (biasanya `ctx`) untuk mengirim pesan ke klien.

<details>
<summary>Python</summary>

<details>
<summary>Python</summary>

```python
@mcp.tool(description="A tool that sends progress notifications")
async def process_files(message: str, ctx: Context) -> TextContent:
    await ctx.info("Processing file 1/3...")
    await ctx.info("Processing file 2/3...")
    await ctx.info("Processing file 3/3...")
    return TextContent(type="text", text=f"Done: {message}")
```

Dalam contoh sebelumnya, metode `process_files` tool sends three notifications to the client as it processes each file. The `ctx.info()` method is used to send informational messages.

</details>

Additionally, to enable notifications, ensure your server uses a streaming transport (like `streamable-http`) and your client implements a message handler to process notifications. Here's how you can set up the server to use the `streamable-http` transport:

```python
mcp.run(transport="streamable-http")
```

</details>

<details>
<summary>.NET</summary>

```csharp
[Tool("A tool that sends progress notifications")]
public async Task<TextContent> ProcessFiles(string message, ToolContext ctx)
{
    await ctx.Info("Processing file 1/3...");
    await ctx.Info("Processing file 2/3...");
    await ctx.Info("Processing file 3/3...");
    return new TextContent
    {
        Type = "text",
        Text = $"Done: {message}"
    };
}
```

Dalam contoh .NET ini, metode `ProcessFiles` tool is decorated with the `Tool` attribute and sends three notifications to the client as it processes each file. The `ctx.Info()` digunakan untuk mengirim pesan informasi.

Untuk mengaktifkan notifikasi di server MCP .NET Anda, pastikan menggunakan transport streaming:

```csharp
var builder = McpBuilder.Create();
await builder
    .UseStreamableHttp() // Enable streamable HTTP transport
    .Build()
    .RunAsync();
```

</details>

### Sisi Klien: Menerima Notifikasi

Klien harus mengimplementasikan handler pesan untuk memproses dan menampilkan notifikasi saat tiba.

<details>
<summary>Python</summary>

```python
async def message_handler(message):
    if isinstance(message, types.ServerNotification):
        print("NOTIFICATION:", message)
    else:
        print("SERVER MESSAGE:", message)

async with ClientSession(
   read_stream, 
   write_stream,
   logging_callback=logging_collector,
   message_handler=message_handler,
) as session:
```

Dalam kode sebelumnya, `message_handler` function checks if the incoming message is a notification. If it is, it prints the notification; otherwise, it processes it as a regular server message. Also note how the `ClientSession` is initialized with the `message_handler` digunakan untuk menangani notifikasi masuk.

</details>

<details>
<summary>.NET</summary>

```csharp
// Define a message handler
void MessageHandler(IJsonRpcMessage message)
{
    if (message is ServerNotification notification)
    {
        Console.WriteLine($"NOTIFICATION: {notification}");
    }
    else
    {
        Console.WriteLine($"SERVER MESSAGE: {message}");
    }
}

// Create and use a client session with the message handler
var clientOptions = new ClientSessionOptions
{
    MessageHandler = MessageHandler,
    LoggingCallback = (level, message) => Console.WriteLine($"[{level}] {message}")
};

using var client = new ClientSession(readStream, writeStream, clientOptions);
await client.InitializeAsync();

// Now the client will process notifications through the MessageHandler
```

Dalam contoh .NET ini, `MessageHandler` function checks if the incoming message is a notification. If it is, it prints the notification; otherwise, it processes it as a regular server message. The `ClientSession` is initialized with the message handler via the `ClientSessionOptions`.

</details>

To enable notifications, ensure your server uses a streaming transport (like `streamable-http`) dan klien Anda mengimplementasikan handler pesan untuk memproses notifikasi.

## Notifikasi Kemajuan & Skenario

Bagian ini menjelaskan konsep notifikasi kemajuan dalam MCP, mengapa penting, dan cara mengimplementasikannya menggunakan Streamable HTTP. Anda juga akan menemukan tugas praktis untuk memperkuat pemahaman.

Notifikasi kemajuan adalah pesan real-time yang dikirim dari server ke klien selama operasi yang berjalan lama. Alih-alih menunggu proses selesai, server terus memperbarui klien tentang status terkini. Ini meningkatkan transparansi, pengalaman pengguna, dan memudahkan debugging.

**Contoh:**

```text

"Processing document 1/10"
"Processing document 2/10"
...
"Processing complete!"

```

### Mengapa Menggunakan Notifikasi Kemajuan?

Notifikasi kemajuan penting karena beberapa alasan:

- **Pengalaman pengguna lebih baik:** Pengguna melihat pembaruan saat pekerjaan berlangsung, bukan hanya di akhir.
- **Umpan balik real-time:** Klien dapat menampilkan progress bar atau log, membuat aplikasi terasa responsif.
- **Memudahkan debugging dan pemantauan:** Pengembang dan pengguna dapat melihat di mana proses mungkin lambat atau terhenti.

### Cara Mengimplementasikan Notifikasi Kemajuan

Berikut cara mengimplementasikan notifikasi kemajuan di MCP:

- **Di server:** Gunakan `ctx.info()` or `ctx.log()` untuk mengirim notifikasi saat setiap item diproses. Ini mengirim pesan ke klien sebelum hasil utama siap.
- **Di klien:** Implementasikan handler pesan yang mendengarkan dan menampilkan notifikasi saat tiba. Handler ini membedakan antara notifikasi dan hasil akhir.

**Contoh Server:**

<details>
<summary>Python</summary>

```python
@mcp.tool(description="A tool that sends progress notifications")
async def process_files(message: str, ctx: Context) -> TextContent:
    for i in range(1, 11):
        await ctx.info(f"Processing document {i}/10")
    await ctx.info("Processing complete!")
    return TextContent(type="text", text=f"Done: {message}")
```

</details>

**Contoh Klien:**

<details>
<summary>Python</summary>

```python
async def message_handler(message):
    if isinstance(message, types.ServerNotification):
        print("NOTIFICATION:", message)
    else:
        print("SERVER MESSAGE:", message)
```

</details>

## Pertimbangan Keamanan

Saat mengimplementasikan server MCP dengan transport berbasis HTTP, keamanan menjadi perhatian utama yang memerlukan perhatian cermat terhadap berbagai vektor serangan dan mekanisme perlindungan.

### Gambaran Umum

Keamanan sangat penting saat mengekspos server MCP melalui HTTP. Streamable HTTP memperkenalkan permukaan serangan baru dan memerlukan konfigurasi yang hati-hati.

### Poin Penting
- **Validasi Header Origin**: Selalu validasi `Origin` header to prevent DNS rebinding attacks.
- **Localhost Binding**: For local development, bind servers to `localhost` to avoid exposing them to the public internet.
- **Authentication**: Implement authentication (e.g., API keys, OAuth) for production deployments.
- **CORS**: Configure Cross-Origin Resource Sharing (CORS) policies to restrict access.
- **HTTPS**: Use HTTPS in production to encrypt traffic.

### Best Practices
- Never trust incoming requests without validation.
- Log and monitor all access and errors.
- Regularly update dependencies to patch security vulnerabilities.

### Challenges
- Balancing security with ease of development
- Ensuring compatibility with various client environments


## Upgrading from SSE to Streamable HTTP

For applications currently using Server-Sent Events (SSE), migrating to Streamable HTTP provides enhanced capabilities and better long-term sustainability for your MCP implementations.

### Why Upgrade?
- Streamable HTTP offers better scalability, compatibility, and richer notification support than SSE.
- It is the recommended transport for new MCP applications.

### Migration Steps
- **Update server code** to use `transport="streamable-http"` in `mcp.run()`.
- **Update client code** to use `streamablehttp_client` instead of SSE client.
- **Implement a message handler** in the client to process notifications.
- **Test for compatibility** with existing tools and workflows.

### Maintaining Compatibility
- You can support both SSE and Streamable HTTP by running both transports on different endpoints.
- Gradually migrate clients to the new transport.

### Challenges
- Ensuring all clients are updated
- Handling differences in notification delivery

## Security Considerations

Security should be a top priority when implementing any server, especially when using HTTP-based transports like Streamable HTTP in MCP. 

When implementing MCP servers with HTTP-based transports, security becomes a paramount concern that requires careful attention to multiple attack vectors and protection mechanisms.

### Overview

Security is critical when exposing MCP servers over HTTP. Streamable HTTP introduces new attack surfaces and requires careful configuration.

Here are some key security considerations:

- **Origin Header Validation**: Always validate the `Origin` header to prevent DNS rebinding attacks.
- **Localhost Binding**: For local development, bind servers to `localhost` to avoid exposing them to the public internet.
- **Authentication**: Implement authentication (e.g., API keys, OAuth) for production deployments.
- **CORS**: Configure Cross-Origin Resource Sharing (CORS) policies to restrict access.
- **HTTPS**: Use HTTPS in production to encrypt traffic.

### Best Practices

Additionally, here are some best practices to follow when implementing security in your MCP streaming server:

- Never trust incoming requests without validation.
- Log and monitor all access and errors.
- Regularly update dependencies to patch security vulnerabilities.

### Challenges

You will face some challenges when implementing security in MCP streaming servers:

- Balancing security with ease of development
- Ensuring compatibility with various client environments


## Upgrading from SSE to Streamable HTTP

For applications currently using Server-Sent Events (SSE), migrating to Streamable HTTP provides enhanced capabilities and better long-term sustainability for your MCP implementations.

### Why Upgrade?

There are two compelling reasons to upgrade from SSE to Streamable HTTP:

- Streamable HTTP offers better scalability, compatibility, and richer notification support than SSE.
- It is the recommended transport for new MCP applications.

### Migration Steps

Here's how you can migrate from SSE to Streamable HTTP in your MCP applications:

1. **Update server code** to use `transport="streamable-http"` in `mcp.run()`.
2. **Update client code** to use `streamablehttp_client` daripada klien SSE.
3. **Implementasikan handler pesan** di klien untuk memproses notifikasi.
4. **Uji kompatibilitas** dengan alat dan alur kerja yang ada.

### Mempertahankan Kompatibilitas

Disarankan untuk mempertahankan kompatibilitas dengan klien SSE yang sudah ada selama proses migrasi. Berikut beberapa strategi:

- Anda dapat mendukung kedua transport SSE dan Streamable HTTP dengan menjalankan keduanya pada endpoint berbeda.
- Migrasi klien secara bertahap ke transport baru.

### Tantangan

Pastikan Anda mengatasi tantangan berikut selama migrasi:

- Memastikan semua klien diperbarui.
- Menangani perbedaan dalam pengiriman notifikasi.

### Tugas: Bangun Aplikasi Streaming MCP Anda Sendiri

**Skenario:**
Bangun server dan klien MCP di mana server memproses daftar item (misalnya, file atau dokumen) dan mengirim notifikasi untuk setiap item yang diproses. Klien harus menampilkan setiap notifikasi saat tiba.

**Langkah-langkah:**

1. Implementasikan tool server yang memproses daftar dan mengirim notifikasi untuk setiap item.
2. Implementasikan klien dengan handler pesan untuk menampilkan notifikasi secara real-time.
3. Uji implementasi Anda dengan menjalankan server dan klien, dan amati notifikasi yang muncul.

[Solution](./solution/README.md)

## Bacaan Lanjutan & Langkah Selanjutnya?

Untuk melanjutkan perjalanan Anda dengan streaming MCP dan memperluas pengetahuan, bagian ini menyediakan sumber daya tambahan dan langkah yang disarankan untuk membangun aplikasi yang lebih canggih.

### Bacaan Lanjutan

- [Microsoft: Pengenalan HTTP Streaming](https://learn.microsoft.com/aspnet/core/fundamentals/http-requests?view=aspnetcore-8.0&WT.mc_id=%3Fwt.mc_id%3DMVP_452430#streaming)
- [Microsoft: Server-Sent Events (SSE)](https://learn.microsoft.com/azure/application-gateway/for-containers/server-sent-events?tabs=server-sent-events-gateway-api&WT.mc_id=%3Fwt.mc_id%3DMVP_452430)
- [Microsoft: CORS di ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/cors?view=aspnetcore-8.0&WT.mc_id=%3Fwt.mc_id%3DMVP_452430)
- [Python requests: Streaming Requests](https://requests.readthedocs.io/en/latest/user/advanced/#streaming-requests)

### Langkah Selanjutnya?

- Coba bangun tool MCP yang lebih canggih yang menggunakan streaming untuk analitik real-time, chat, atau pengeditan kolaboratif.
- Jelajahi integrasi streaming MCP dengan framework frontend (React, Vue, dll.) untuk pembaruan UI secara langsung.
- Selanjutnya: [Memanfaatkan AI Toolkit untuk VSCode](../07-aitk/README.md)

**Penafian**:  
Dokumen ini telah diterjemahkan menggunakan layanan terjemahan AI [Co-op Translator](https://github.com/Azure/co-op-translator). Meskipun kami berupaya untuk akurasi, harap diingat bahwa terjemahan otomatis mungkin mengandung kesalahan atau ketidakakuratan. Dokumen asli dalam bahasa aslinya harus dianggap sebagai sumber yang sah. Untuk informasi penting, disarankan menggunakan terjemahan profesional oleh manusia. Kami tidak bertanggung jawab atas kesalahpahaman atau salah tafsir yang timbul dari penggunaan terjemahan ini.