---
description: 完整的 v0 示例——使用 API 密钥购买能量，涵盖从检查订单簿到轮询履约的全过程，提供 JavaScript、Java、Ruby 和 PHP 版本。
---

# v0 — 使用 API 密钥购买

{% hint style="warning" %}
**旧版示例。** 本页记录的是 `v0` API 端点。新的集成应使用 [身份验证](../authentication.md) 和 [API 参考](../api-reference/buy-resources/api-key/) 中描述的当前 API 密钥端点。下方的 `v0` 代码仅为现有集成而保留。
{% endhint %}

这是一个完整、可运行的示例，演示如何通过 API 密钥购买能量。它会检查 [订单簿](../api-reference/buy-resources/api-key/get-order-book.md)，验证你的内部账户余额，创建订单，然后轮询该订单直到其履约完成。

## 获取 API 密钥

在运行此示例之前，你需要一个与 TronSave 内部账户绑定的 API 密钥。请参阅 [身份验证](../authentication.md) 了解生成 API 密钥的两种方式（在网站上或通过 Telegram）。

## 前置要求

JavaScript 版本使用 TronWeb。请安装指定的版本：

```bash
npm i tronweb@5.3.2 @noble/secp256k1@1.7.1
```

{% hint style="info" %}
必须使用 TronWeb **5.3.2**。详情请阅读 [TronWeb 5.3.2 发行说明](https://tronweb.network/docu/docs/5.3.2/Release%20Note/)。
{% endhint %}

## 配置

替换每个文件顶部的占位常量：

| 常量                   | 说明                                                                  |
| -------------------- | ------------------------------------------------------------------- |
| `API_KEY`            | 你的 TronSave API 密钥。                                                 |
| `TRONSAVE_API_URL`   | 主网使用 `https://api.tronsave.io`，测试网使用 `https://api-dev.tronsave.io`。 |
| `RECEIVER_ADDRESS`   | 将接收能量的接收地址。                                                         |
| `BUY_AMOUNT`         | 要购买的能量数量。                                                           |
| `DURATION`           | 订单时长（以毫秒为单位）。示例中的默认值：`3600 * 1000`（1 小时）。                           |
| `MAX_PRICE_ACCEPTED` | 你愿意支付的最高价格（以每单位能量的 SUN 计）。                                          |

## 完整示例

{% tabs %}
{% tab title="Javascript" %}
```javascript
const API_KEY = `your_api_key`; // CHANGE ME
const TRONSAVE_API_URL = "https://api.tronsave.io" //in testnet mode change it to "https://api-dev.tronsave.io"
const RECEIVER_ADDRESS = 'your_receiver_address' // CHANGE ME
const BUY_AMOUNT = 100000 // CHANGE ME
const DURATION = 3600 * 1000 // current value: 1h. CHANGE ME
const MAX_PRICE_ACCEPTED = 100 // CHANGE ME

const sleep = async (ms) => {
    await new Promise((resolver, reject) => {
        setTimeout(() => resolver("OK"), ms)
    })
}

const GetOrderBook = async (api_key, receiver_address) => {
    const url = `${TRONSAVE_API_URL}/v0/order-book?address=${receiver_address}`
    const data = await fetch(url, {
        headers: {
            'apikey': api_key
        }
    })
    const response = await data.json()
    /**
     * Example response 
     * @link https://docs.tronsave.io/buy-energy-on-telegram/using-api-key-to/get-order-book
      [
        {
            "price": -1,
            "available_energy_amount": 177451
        },
        {
            "price": 30,
            "available_energy_amount": 331088
        },
        {
            "price": 35,
            "available_energy_amount": 2841948
        },
    ]
     */
    return response
}

const GetAccountInfo = async (api_key) => {
    const url = `${TRONSAVE_API_URL}/v0/user-info`
    const data = await fetch(url, {
        headers: {
            'apikey': api_key
        }
    })
    const response = await data.json()
    /**
     * Example response 
     * @link https://docs.tronsave.io/buy-energy-on-telegram/using-api-key-to/get-internal-account-info
    {
        "id": "user_id",
        "balance": "1000000",
        "represent_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
        "deposit_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
    }
     */
    return response
}


const BuyEnergy = async (api_key, target_address, amount, duration_ms, max_price_accepted) => {
    const url = `${TRONSAVE_API_URL}/v0/internal-buy-energy`
    //see more at https://docs.tronsave.io/buy-energy-on-telegram/using-api-key-to/buy-energy
    const body = {
        "resource_type": "ENERGY",
        "buy_energy_type": "MEDIUM", //price in sun or "SLOW"|"MEDIUM"|"FAST"
        "amount": amount, //Amount of resource want to buy
        "allow_partial_fill": true,
        "target_address": target_address,
        "duration_millisec": duration_ms, //order duration in milli sec. Default: 259200000 (3 days)
        "only_create_when_fulfilled": false,
        "max_price_accepted": max_price_accepted,
        "add_order_incomplete": false
    }
    const data = await fetch(url, {
        method: "POST",
        headers: {
            'apikey': api_key,
            "content-type": "application/json",
        },
        body: JSON.stringify(body)
    })
    const response = await data.json()
    /**
     * Example response 
     * @link  https://docs.tronsave.io/buy-energy-on-telegram/using-api-key-to/buy-energy
   {
      "order_id": "651d2306e55c073f6ca0992e",
      "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
      "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
      "resource_amount": 100000,
      "resource_type": "ENERGY",
      "remain_amount": 0,
      "price": 67.5,
      "duration": 3600,
      "allow_partial_fill": true,
      "payout_amount": 6750000,
      "fulfilled_percent": 100
}
     */
    return response
}

const GetOneOrderDetails = async (api_key, order_id) => {
    const url = `${TRONSAVE_API_URL}/v0/orders/${order_id}`
    const data = await fetch(url, {
        headers: {
            'apikey': api_key
        }
    })
    const response = await data.json()
    /**
     * Example response 
     * @link https://docs.tronsave.io/buy-energy-on-telegram/using-api-key-to/get-internal-account-order-history
        {
            "id": "651d2306e55c073f6ca0992e",
            "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
            "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
            "resource_amount": 100000,
            "resource_type": "ENERGY",
            "remain_amount": 0,
            "price": 67.5,
            "duration": 3600,
            "allow_partial_fill": true,
            "payout_amount": 6750000,
            "fulfilled_percent": 100,
            "matched_delegates": [
                        {
                            "delegator": "TKVSaJQDWeKFSEXmA44pjxduGTxy888888",
                            "amount": 100000,
                            "txid": "transaction_id_1"
                        }
                    ]
        }
     */
    return response
}

const CreateOrderByUsingApiKey = async () => {
    //Check energy available
    const order_book = await GetOrderBook(API_KEY, RECEIVER_ADDRESS)
    console.log(order_book)
    /*
      [
        {
            "price": -1,
            "available_energy_amount": 177451
        },
        {
            "price": 30,
            "available_energy_amount": 331088
        },
        {
            "price": 35,
            "available_energy_amount": 2841948
        },
    ]
    */
    //Look at response above, we have 177k energy at price less than 30, 331k enegy at price 30 and 2841k energy at price 35
    //Example if want to buy 500k energy in 3 days you have to place order at price at least 35 energy to fulfill your order (the price can be higher if duration of order less than 3 days)

    const need_trx = MAX_PRICE_ACCEPTED * BUY_AMOUNT

    //Check if your internal balance enough to buy
    const account_info = await GetAccountInfo(API_KEY)
    console.log(account_info)
    /*
     {
        "id": "user_id",
        "balance": "1000000",
        "represent_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
        "deposit_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
    }
    */
    const is_balance_enough = Number(account_info.balance) >= need_trx
    if (is_balance_enough) {

        const buy_energy_order = await BuyEnergy(API_KEY, RECEIVER_ADDRESS, BUY_AMOUNT, DURATION, MAX_PRICE_ACCEPTED)
        console.log(buy_energy_order)
        /*
          {
          "order_id": "651d2306e55c073f6ca0992e",
          "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
          "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
          "resource_amount": 100000,
          "resource_type": "ENERGY",
          "remain_amount": 0,
          "price": 67.5,
          "duration": 3600,
          "allow_partial_fill": true,
          "payout_amount": 6750000,
          "fulfilled_percent": 100
        }
        */
        //Wait 3-5 seconds after buy then check 
        if (buy_energy_order.order_id) {
            while (true) {
                await sleep(3000)
                const order_details = await GetOneOrderDetails(API_KEY, buy_energy_order.order_id)
                console.log(order_details)
                /*
                    {
                        "id": "651d2306e55c073f6ca0992e",
                        "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
                        "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
                        "resource_amount": 100000,
                        "resource_type": "ENERGY",
                        "remain_amount": 0,
                        "price": 67.5,
                        "duration": 3600,
                        "allow_partial_fill": true,
                        "payout_amount": 6750000,
                        "fulfilled_percent": 100,
                        "matched_delegates": [
                                    {
                                        "delegator": "TKVSaJQDWeKFSEXmA44pjxduGTxy888888",
                                        "amount": 100000,
                                        "txid": "transaction_id_1"
                                    }
                                ]
                    }
                */
                if (order_details && order_details.fulfilled_percent === 100 || order_details.remain_amount === 0) {
                    console.log(`Your order already fulfilled`)
                    break;
                } else {
                    console.log(`Your order is not fulfilled, wait 3s and recheck`)
                }
            }
        } else {
            console.log({ buy_energy_order })
            throw new Error(`Buy Order Failed`)
        }
    }
}

CreateOrderByUsingApiKey()
```
{% endtab %}

{% tab title="Java" %}
```java
import java.io.IOException;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.Scanner;

public class Main {
    static final String API_KEY = "your_api_key"; // CHANGE ME
    static final String TRONSAVE_API_URL = "https://api.tronsave.io"; // in testnet mode change it to "https://api-dev.tronsave.io"
    static final String RECEIVER_ADDRESS = "your_receiver_address"; // CHANGE ME
    static final int BUY_AMOUNT = 100000; // CHANGE ME
    static final long DURATION = 3600 * 1000; // current value: 1h. CHANGE ME
    static final int MAX_PRICE_ACCEPTED = 100; // CHANGE ME

    public static void main(String[] args) {
        try {
            createOrderByUsingApiKey();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    static void createOrderByUsingApiKey() throws IOException {
        // Check energy available
        String orderBookJson = getOrderBook(API_KEY, RECEIVER_ADDRESS);
        System.out.println(orderBookJson);

        // Look at response above, we have 177k energy at price less than 30, 331k energy at price 30, and 2841k energy at price 35
        // Example if you want to buy 500k energy in 3 days you have to place an order at a price of at least 35 energy to fulfill your order
        // (the price can be higher if the duration of order is less than 3 days)

        int needTrx = MAX_PRICE_ACCEPTED * BUY_AMOUNT;

        // Check if your internal balance enough to buy
        String accountInfoJson = getAccountInfo(API_KEY);
        System.out.println(accountInfoJson);

        // Parse JSON to check if balance is enough
        // Here, you need to implement JSON parsing

        // Assuming balance check is successful, proceed to buying energy
        String buyEnergyOrderJson = buyEnergy(API_KEY, RECEIVER_ADDRESS, BUY_AMOUNT, DURATION, MAX_PRICE_ACCEPTED);
        System.out.println(buyEnergyOrderJson);

        // Wait 3-5 seconds after buy then check
        // Here, you need to implement a waiting mechanism

        // Assuming the order is fulfilled, you would receive a response similar to the following:
        // {
        //    "order_id": "651d2306e55c073f6ca0992e",
        //    "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
        //    "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
        //    "resource_amount": 100000,
        //    "resource_type": "ENERGY",
        //    "remain_amount": 0,
        //    "price": 67.5,
        //    "duration": 3600,
        //    "allow_partial_fill": true,
        //    "payout_amount": 6750000,
        //    "fulfilled_percent": 100
        // }

        // Here, you would need to implement the logic to continuously check the order status until it's fulfilled
    }

    static String getOrderBook(String apiKey, String receiverAddress) throws IOException {
        URL url = new URL(TRONSAVE_API_URL + "/v0/order-book?address=" + receiverAddress);
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod("GET");
        conn.setRequestProperty("apikey", apiKey);
        conn.connect();
        Scanner scanner = new Scanner(conn.getInputStream());
        StringBuilder response = new StringBuilder();
        while (scanner.hasNextLine()) {
            response.append(scanner.nextLine());
        }
        return response.toString();
    }

    static String getAccountInfo(String apiKey) throws IOException {
        URL url = new URL(TRONSAVE_API_URL + "/v0/user-info");
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod("GET");
        conn.setRequestProperty("apikey", apiKey);
        conn.connect();
        Scanner scanner = new Scanner(conn.getInputStream());
        StringBuilder response = new StringBuilder();
        while (scanner.hasNextLine()) {
            response.append(scanner.nextLine());
        }
        return response.toString();
    }

    static String buyEnergy(String apiKey, String targetAddress, int amount, long durationMs, int maxPriceAccepted) throws IOException {
        URL url = new URL(TRONSAVE_API_URL + "/v0/internal-buy-energy");
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod("POST");
        conn.setRequestProperty("apikey", apiKey);
        conn.setRequestProperty("content-type", "application/json");
        conn.setDoOutput(true);
        String body = "{\n" +
                "        \"resource_type\": \"ENERGY\",\n" +
                "        \"buy_energy_type\": \"MEDIUM\",\n" +
                "        \"amount\": " + amount + ",\n" +
                "        \"allow_partial_fill\": true,\n" +
                "        \"target_address\": \"" + targetAddress + "\",\n" +
                "        \"duration_millisec\": " + durationMs + ",\n" +
                "        \"only_create_when_fulfilled\": false,\n" +
                "        \"max_price_accepted\": " + maxPriceAccepted + ",\n" +
                "        \"add_order_incomplete\": false\n" +
                "    }";
        conn.getOutputStream().write(body.getBytes());
        conn.connect();
        Scanner scanner = new Scanner(conn.getInputStream());
        StringBuilder response = new StringBuilder();
        while (scanner.hasNextLine()) {
            response.append(scanner.nextLine());
        }
        return response.toString();
    }
}
```
{% endtab %}

{% tab title="Ruby" %}
```ruby
require 'net/http'
require 'uri'
require 'json'

API_KEY = 'your_api_key' # CHANGE ME
TRONSAVE_API_URL = 'https://api.tronsave.io' # in testnet mode change it to 'https://api-dev.tronsave.io'
RECEIVER_ADDRESS = 'your_receiver_address' # CHANGE ME
BUY_AMOUNT = 100_000 # CHANGE ME
DURATION = 3_600 * 1_000 # current value: 1h. CHANGE ME
MAX_PRICE_ACCEPTED = 100 # CHANGE ME

def sleep_ms(ms)
  sleep(ms / 1_000.0)
end

def get_order_book(api_key, receiver_address)
  url = URI.parse("#{TRONSAVE_API_URL}/v0/order-book?address=#{receiver_address}")
  http = Net::HTTP.new(url.host, url.port)
  http.use_ssl = true
  request = Net::HTTP::Get.new(url)
  request['apikey'] = api_key
  response = http.request(request)
  JSON.parse(response.body)
end

def get_account_info(api_key)
  url = URI.parse("#{TRONSAVE_API_URL}/v0/user-info")
  http = Net::HTTP.new(url.host, url.port)
  http.use_ssl = true
  request = Net::HTTP::Get.new(url)
  request['apikey'] = api_key
  response = http.request(request)
  JSON.parse(response.body)
end

def buy_energy(api_key, target_address, amount, duration_ms, max_price_accepted)
  url = URI.parse("#{TRONSAVE_API_URL}/v0/internal-buy-energy")
  http = Net::HTTP.new(url.host, url.port)
  http.use_ssl = true
  request = Net::HTTP::Post.new(url)
  request['apikey'] = api_key
  request['content-type'] = 'application/json'
  body = {
    "resource_type": "ENERGY",
    "buy_energy_type": "MEDIUM",
    "amount": amount,
    "allow_partial_fill": true,
    "target_address": target_address,
    "duration_millisec": duration_ms,
    "only_create_when_fulfilled": false,
    "max_price_accepted": max_price_accepted,
    "add_order_incomplete": false
  }
  request.body = body.to_json
  response = http.request(request)
  JSON.parse(response.body)
end

def get_one_order_details(api_key, order_id)
  url = URI.parse("#{TRONSAVE_API_URL}/v0/orders/#{order_id}")
  http = Net::HTTP.new(url.host, url.port)
  http.use_ssl = true
  request = Net::HTTP::Get.new(url)
  request['apikey'] = api_key
  response = http.request(request)
  JSON.parse(response.body)
end

def create_order_by_using_api_key
  # Check energy available
  order_book = get_order_book(API_KEY, RECEIVER_ADDRESS)
  puts order_book

  # Look at response above, we have 177k energy at price less than 30, 331k energy at price 30, and 2841k energy at price 35
  # Example if you want to buy 500k energy in 3 days you have to place an order at a price of at least 35 energy to fulfill your order
  # (the price can be higher if the duration of order is less than 3 days)

  need_trx = MAX_PRICE_ACCEPTED * BUY_AMOUNT

  # Check if your internal balance enough to buy
  account_info = get_account_info(API_KEY)
  puts account_info

  # Parse JSON to check if balance is enough
  # Here, you need to implement JSON parsing

  # Assuming balance check is successful, proceed to buying energy
  buy_energy_order = buy_energy(API_KEY, RECEIVER_ADDRESS, BUY_AMOUNT, DURATION, MAX_PRICE_ACCEPTED)
  puts buy_energy_order

  # Wait 3-5 seconds after buy then check
  # Here, you need to implement a waiting mechanism

  # Assuming the order is fulfilled, you would receive a response similar to the following:
  # {
  #    "order_id": "651d2306e55c073f6ca0992e",
  #    "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
  #    "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
  #    "resource_amount": 100000,
  #    "resource_type": "ENERGY",
  #    "remain_amount": 0,
  #    "price": 67.5,
  #    "duration": 3600,
  #    "allow_partial_fill": true,
  #    "payout_amount": 6750000,
  #    "fulfilled_percent": 100
  # }

  # Here, you would need to implement the logic to continuously check the order status until it's fulfilled
end

create_order_by_using_api_key
```
{% endtab %}

{% tab title="PHP" %}
```php
<?php
$API_KEY = 'your_api_key'; // CHANGE ME
$TRONSAVE_API_URL = 'https://api.tronsave.io'; // in testnet mode change it to 'https://api-dev.tronsave.io'
$RECEIVER_ADDRESS = 'your_receiver_address'; // CHANGE ME
$BUY_AMOUNT = 100000; // CHANGE ME
$DURATION = 3600 * 1000; // current value: 1h. CHANGE ME
$MAX_PRICE_ACCEPTED = 100; // CHANGE ME

function sleep_ms($ms) {
    usleep($ms * 1000);
}

function get_order_book($api_key, $receiver_address) {
    global $TRONSAVE_API_URL;
    $url = $TRONSAVE_API_URL . "/v0/order-book?address=" . $receiver_address;
    $options = array(
        'http' => array(
            'header' => "apikey: $api_key\r\n",
            'method' => 'GET'
        )
    );
    $context = stream_context_create($options);
    $data = file_get_contents($url, false, $context);
    return json_decode($data, true);
}

function get_account_info($api_key) {
    global $TRONSAVE_API_URL;
    $url = $TRONSAVE_API_URL . "/v0/user-info";
    $options = array(
        'http' => array(
            'header' => "apikey: $api_key\r\n",
            'method' => 'GET'
        )
    );
    $context = stream_context_create($options);
    $data = file_get_contents($url, false, $context);
    return json_decode($data, true);
}

function buy_energy($api_key, $target_address, $amount, $duration_ms, $max_price_accepted) {
    global $TRONSAVE_API_URL;
    $url = $TRONSAVE_API_URL . "/v0/internal-buy-energy";
    $body = json_encode(array(
        "resource_type" => "ENERGY",
        "buy_energy_type" => "MEDIUM",
        "amount" => $amount,
        "allow_partial_fill" => true,
        "target_address" => $target_address,
        "duration_millisec" => $duration_ms,
        "only_create_when_fulfilled" => false,
        "max_price_accepted" => $max_price_accepted,
        "add_order_incomplete" => false
    ));
    $options = array(
        'http' => array(
            'header' => "Content-type: application/json\r\n" .
                        "apikey: $api_key\r\n",
            'method' => 'POST',
            'content' => $body
        )
    );
    $context = stream_context_create($options);
    $data = file_get_contents($url, false, $context);
    return json_decode($data, true);
}

function get_one_order_details($api_key, $order_id) {
    global $TRONSAVE_API_URL;
    $url = $TRONSAVE_API_URL . "/v0/orders/" . $order_id;
    $options = array(
        'http' => array(
            'header' => "apikey: $api_key\r\n",
            'method' => 'GET'
        )
    );
    $context = stream_context_create($options);
    $data = file_get_contents($url, false, $context);
    return json_decode($data, true);
}

function create_order_by_using_api_key() {
    global $API_KEY, $RECEIVER_ADDRESS, $BUY_AMOUNT, $DURATION, $MAX_PRICE_ACCEPTED;
    // Check energy available
    $order_book = get_order_book($API_KEY, $RECEIVER_ADDRESS);
    print_r($order_book);
    /*
      [
        {
            "price": -1,
            "available_energy_amount": 177451
        },
        {
            "price": 30,
            "available_energy_amount": 331088
        },
        {
            "price": 35,
            "available_energy_amount": 2841948
        },
    ]
    */
    // Look at response above, we have 177k energy at price less than 30, 331k energy at price 30, and 2841k energy at price 35
    // Example if you want to buy 500k energy in 3 days you have to place an order at a price of at least 35 energy to fulfill your order
    // (the price can be higher if the duration of order is less than 3 days)

    $need_trx = $MAX_PRICE_ACCEPTED * $BUY_AMOUNT;

    // Check if your internal balance enough to buy
    $account_info = get_account_info($API_KEY);
    print_r($account_info);
    /*
     {
        "id": "user_id",
        "balance": "1000000",
        "represent_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
        "deposit_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
    }
    */
    $is_balance_enough = intval($account_info["balance"]) >= $need_trx;
    if ($is_balance_enough) {
        $buy_energy_order = buy_energy($API_KEY, $RECEIVER_ADDRESS, $BUY_AMOUNT, $DURATION, $MAX_PRICE_ACCEPTED);
        print_r($buy_energy_order);
        /*
          {
          "order_id": "651d2306e55c073f6ca0992e",
          "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
          "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
          "resource_amount": 100000,
          "resource_type": "ENERGY",
          "remain_amount": 0,
          "price": 67.5,
          "duration": 3600,
          "allow_partial_fill": true,
          "payout_amount": 6750000,
          "fulfilled_percent": 100
        }
        */
        // Wait 3-5 seconds after buy then check
        if ($buy_energy_order["order_id"]) {
            while (true) {
                sleep_ms(3000);
                $order_details = get_one_order_details($API_KEY, $buy_energy_order["order_id"]);
                print_r($order_details);
                /*
                    {
                        "id": "651d2306e55c073f6ca0992e",
                        "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
                        "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
                        "resource_amount": 100000,
                        "resource_type": "ENERGY",
                        "remain_amount": 0,
                        "price": 67.5,
                        "duration": 3600,
                        "allow_partial_fill": true,
                        "payout_amount": 6750000,
                        "fulfilled_percent": 100,
                        "matched_delegates": [
                                    {
                                        "delegator": "TKVSaJQDWeKFSEXmA44pjxduGTxy888888",
                                        "amount": 100000,
                                        "txid": "transaction_id_1"
                                    }
                                ]
                    }
                */
                if ($order_details && ($order_details["fulfilled_percent"] === 100 || $order_details["remain_amount"] === 0)) {
                    echo "Your order already fulfilled\n";
                    break;
                } else {
                    echo "Your order is not fulfilled, wait 3s and recheck\n";
                }
            }
        } else {
            print_r($buy_energy_order);
            throw new Exception("Buy Order Failed");
        }
    }
}

create_order_by_using_api_key();
?>
```
{% endtab %}
{% endtabs %}

## 工作原理

1. **检查订单簿**——`GET /v0/order-book` 返回按价格分组的可用能量。`price` 为 `-1` 表示价格低于最低价格档位的可用能量。利用它来选择一个能够履约你的 `BUY_AMOUNT` 的 `MAX_PRICE_ACCEPTED`。
2. **检查你的余额**——`GET /v0/user-info` 返回你的内部账户 `balance`（以 SUN 计）。本示例仅在 `balance >= MAX_PRICE_ACCEPTED * BUY_AMOUNT` 时才继续执行。
3. **创建订单**——`POST /v0/internal-buy-energy` 下单并返回一个 `order_id`。
4. **轮询履约情况**——每 3 秒轮询一次 `GET /v0/orders/{order_id}`，直到 `fulfilled_percent` 达到 100（或 `remain_amount` 为 0）。

## 后续步骤

* [身份验证](../authentication.md)——生成 API 密钥并为你的内部账户充值。
* [使用 API 密钥购买资源](../api-reference/buy-resources/api-key/)——当前（非 `v0`）的 API 密钥端点。
* [订单类型](../../concepts/order-types.md)——了解价格档位、部分履约和订单时长。
