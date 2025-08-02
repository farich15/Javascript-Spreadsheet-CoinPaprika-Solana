# Javascript-Spreadsheet-Solana

# SolanaUSDT Price

function getSolanaPrice() {
  var cache = CacheService.getScriptCache();
  var cached = cache.get("sol_price");

  if (cached !== null) {
    return parseFloat(cached);
  }

  var response = UrlFetchApp.fetch('https://api.coinpaprika.com/v1/tickers/sol-solana');
  var data = JSON.parse(response.getContentText());
  var price = data.quotes.USD.price;

  cache.put("sol_price", price, 3600);

  return price;
}
----------
# RUN: 
getSolanaPrice()
--------------------

# Aalisa Harga 

function analisaHargaSolana() {
  const cache = CacheService.getScriptCache();
  const cachedData = cache.get("harga_solana");

  let hargaBaru;

  if (cachedData) {
    hargaBaru = Number(cachedData);
  } else {
    const url = "https://api.coingecko.com/api/v3/simple/price?ids=solana&vs_currencies=usd";
    const response = UrlFetchApp.fetch(url);
    const data = JSON.parse(response.getContentText());
    hargaBaru = Number(data.solana.usd);
    cache.put("harga_solana", hargaBaru.toString(), 3600); // cache 1 jam
  }

  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const cellHargaLama = sheet.getRange("B1");
  const hargaLama = Number(cellHargaLama.getValue()) || 0;

  sheet.getRange("B1").setValue(hargaBaru);

  let analisa = '';
  if (hargaBaru > hargaLama) {
    analisa = 'Naik';
  } else if (hargaBaru < hargaLama) {
    analisa = 'Turun';
  } else {
    analisa = 'Stagnan';
  }

  sheet.getRange("C1").setValue(analisa);
}
----------
# RUN: 
analisaHargaSolana()
--------------------

# Convert USD - IDR 

function konversiUSDkeIDR() {
  const cache = CacheService.getScriptCache();
  const cached = cache.get("usd_idr_rate");

  let rate;

  if (cached) {
    rate = Number(cached);
  } else {
    const url = "https://api.exchangerate.host/convert?from=USD&to=IDR";
    const response = UrlFetchApp.fetch(url);
    const data = JSON.parse(response.getContentText());
    rate = Number(data.result);
    cache.put("usd_idr_rate", rate.toString(), 3600); // Cache 1 jam
  }

  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  sheet.getRange("B1").setValue("Kurs USD ke IDR");
  sheet.getRange("B2").setValue(rate);
}
----------
# RUN: 
konversiUSDkeIDR()
--------------------

function analisaTeknikalSolana() {
  const cache = CacheService.getScriptCache();
  const cached = cache.get("harga_solana_teknikal");

  let hargaSekarang;

  if (cached) {
    hargaSekarang = Number(cached);
  } else {
    const url = "https://api.coingecko.com/api/v3/simple/price?ids=solana&vs_currencies=usd";
    const response = UrlFetchApp.fetch(url);
    const data = JSON.parse(response.getContentText());
    hargaSekarang = Number(data.solana.usd);
    cache.put("harga_solana_teknikal", hargaSekarang.toString(), 3600); // 1 jam cache
  }

  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const logRange = sheet.getRange("A2:A4");
  const logData = logRange.getValues();

  // Geser data ke atas, simpan harga baru di A4
  sheet.getRange("A2").setValue(logData[1][0]);
  sheet.getRange("A3").setValue(logData[2][0]);
  sheet.getRange("A4").setValue(hargaSekarang);

  // Hitung SMA
  const sma = (logData[1][0] + logData[2][0] + hargaSekarang) / 3;
sheet.getRange("B4").setValue(sma);

  // Analisa
  let sinyal = "";
  if (hargaSekarang > sma) {
    sinyal = "Naik";
  } else if (hargaSekarang < sma) {
    sinyal = "Turun";
  } else {
    sinyal = "Stagnan";
  }

  sheet.getRange("C4").setValue(sinyal);
}
---------- 
# RUN: 
analisaTeknikalSolana()
-------------------- 
# Cara pakai:
- Buat header di A1: “Log Harga”, B1: “SMA 3”, C1: “Analisa”.
----------------------------------------

# PnL

function hitungPnLdanSinyal() {
  const cache = CacheService.getScriptCache();
  let hargaPasar;

  const cached = cache.get("harga_solana_pnl");
  if (cached) {
    hargaPasar = Number(cached);
  } else {
    const url = "https://api.coingecko.com/api/v3/simple/price?ids=solana&vs_currencies=usd";
    const response = UrlFetchApp.fetch(url);
    const data = JSON.parse(response.getContentText());
    hargaPasar = Number(data.solana.usd);
    cache.put("harga_solana_pnl", hargaPasar.toString(), 3600);
  }

  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const hargaBeli = Number(sheet.getRange("E2").getValue()); // harga beli kamu
  const jumlahKoin = Number(sheet.getRange("F2").getValue()); // jumlah koin

  const pnl = (hargaPasar - hargaBeli) * jumlahKoin;
  sheet.getRange("G2").setValue(pnl);

  // Sinyal trading
  let sinyal = "Hold";
  if (hargaPasar > hargaBeli * 1.05) {
    sinyal = "Sell";
  } else if (hargaPasar < hargaBeli * 0.95) {
    sinyal = "Buy";
  }
  sheet.getRange("H2").setValue(sinyal);

  // Tampilkan harga pasar sekarang
  sheet.getRange("D2").setValue(hargaPasar);
}
----------
# RUN:
hitungPnLdanSinyal()
--------------------
# Cara pakai:
# Di sheet, isi:
- E2: harga beli  
- F2: jumlah koin  
- D2: harga pasar  
- G2: PnL otomatis  
- H2: sinyal trading
------------------------------

