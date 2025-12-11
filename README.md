#include <DHT.h>

#define DHTPIN D2     
#define DHTTYPE DHT11 
#define ALARM_PIN D5 

DHT dht(DHTPIN, DHTTYPE);

const float TEMP_HIGH_LIMIT = 30.0; 
const float HUMID_HIGH_LIMIT = 90.0; 

void setup() {
  Serial.begin(9600); 
  Serial.println("Khoi dong DHT11!");
  
  pinMode(ALARM_PIN, OUTPUT); // Cấu hình chân cảnh báo là Output
  digitalWrite(ALARM_PIN, LOW); // Tắt cảnh báo ban đầu

  dht.begin();
}

void loop() {
  delay(2000);

  float h = dht.readHumidity();
  float t = dht.readTemperature();

  if (isnan(h) || isnan(t)) {
    Serial.println("Loi: Khong doc duoc du lieu tu DHT11!");
    return;
  }

  // --- LOGIC CẢNH BÁO ---
  String trangThai = "Binh thuong";
  bool canhBao = false;

  // Kiểm tra nhiệt độ
  if (t >= TEMP_HIGH_LIMIT) {
    trangThai = "NHIET DO QUA CAO!";
    canhBao = true;
  }
  // Kiểm tra độ ẩm (nếu cần cảnh báo độ ẩm thì bỏ comment dòng dưới)
  // else if (h >= HUMID_HIGH_LIMIT) {
  //   trangThai = "DO AM QUA CAO!";
  //   canhBao = true;
  // }

  // Điều khiển phần cứng (Đèn/Còi)
  if (canhBao) {
    digitalWrite(ALARM_PIN, HIGH); // Bật đèn/còi
  } else {
    digitalWrite(ALARM_PIN, LOW);  // Tắt đèn/còi
  }

  // In kết quả
  Serial.print("Do am: ");
  Serial.print(h);
  Serial.print("% | Nhiet do: ");
  Serial.print(t);
  Serial.print("°C | Trang thai: ");
  Serial.println(trangThai);
  
  // Lưu ý: Bạn cần thêm đoạn code gửi 't' và 'h' lên Firebase ở đây
  // để Web App nhận được dữ liệu.
}
