# Javascript-Spreadsheet-CoinPaprika-Solana
Show Real Time SolanaUSDT Price

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


##Run This At Sel##
getSolanaPrice()
