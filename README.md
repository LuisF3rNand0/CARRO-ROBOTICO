#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "CarritoESP32";
const char* password = "robotplaya";

const uint8_t M1A = 13;
const uint8_t M1B = 12;
const uint8_t M2A = 14;
const uint8_t M2B = 27;
const uint8_t M3A = 26;
const uint8_t M3B = 25;
const uint8_t M4A = 33;
const uint8_t M4B = 32;

WebServer server(80);

void stopAll() {
  for (auto p : {M1A, M1B, M2A, M2B, M3A, M3B, M4A, M4B}) digitalWrite(p, LOW);
}

void driveAll(bool forward) {
  drivePair(M1A, M1B, forward);
  drivePair(M2A, M2B, forward);
  drivePair(M3A, M3B, forward);
  drivePair(M4A, M4B, forward);
}

void drivePair(uint8_t a, uint8_t b, bool forward) {
  digitalWrite(a, forward ? HIGH : LOW);
  digitalWrite(b, forward ? LOW : HIGH);
}

void handleRoot() {
  server.send(200, "text/html",
    R"rawliteral(
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Control del Carrito</title>
    <style>
      body {
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        background: #f4f4f4;
        margin: 0;
        padding: 0;
        text-align: center;
      }

      h1 {
        background-color: #333;
        color: #fff;
        margin: 0;
        padding: 20px;
        font-size: 24px;
      }

      .container {
        padding: 30px;
      }

      .control-buttons {
        display: grid;
        grid-template-columns: 1fr 1fr 1fr;
        gap: 15px;
        max-width: 300px;
        margin: 0 auto;
      }

      button {
        padding: 20px;
        font-size: 20px;
        border: none;
        border-radius: 12px;
        box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        transition: all 0.2s ease;
        background-color: #2196F3;
        color: white;
      }

      button:hover {
        background-color: #1976D2;
        transform: scale(1.05);
      }

      .special-buttons {
        margin-top: 30px;
        display: flex;
        justify-content: center;
        gap: 20px;
      }

      .on-btn {
        background-color: #4CAF50;
      }

      .on-btn:hover {
        background-color: #388E3C;
      }

      .off-btn {
        background-color: #F44336;
      }

      .off-btn:hover {
        background-color: #D32F2F;
      }
    </style>
  </head>
  <body>
    <h1>Control del Carrito</h1>
    <div class="container">
      <div class="control-buttons">
        <div></div>
        <button onclick="cmd('fwd')">↑</button>
        <div></div>

        <button onclick="cmd('left')">←</button>
        <button onclick="cmd('stop')">■</button>
        <button onclick="cmd('right')">→</button>

        <div></div>
        <button onclick="cmd('rev')">↓</button>
        <div></div>
      </div>

      <div class="special-buttons">
        <button class="on-btn" onclick="cmd('on')">Encender</button>
        <button class="off-btn" onclick="cmd('off')">Apagar</button>
      </div>
    </div>

    <script>
      function cmd(c){
        fetch('/' + c);
      }
    </script>
  </body>
</html>
