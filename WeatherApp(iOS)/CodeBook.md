# Swift JSON Decoding Explained

This document explains how JSON decoding works in Swift using the `Decodable` protocol, focusing on how raw JSON data is mapped into Swift structs.

---

## How JSON Decoding Works

When you receive raw JSON data from an API, the process typically follows these steps:

1. Pass the raw data to a parsing function like this:

```swift
func parseJSON(_ weatherData: Data) -> WeatherData? {
    let decoder = JSONDecoder()
    do {
        let decodedData = try decoder.decode(WeatherData.self, from: weatherData)
        return decodedData
    } catch {
        print("Error decoding JSON:", error)
        return nil
    }
}

```

---

## We pass the raw data to the parseJSON(weatherData:) function.
Inside it, the decoder decodes the entire JSON, but only keeps the values that match the property names in our struct — like matching "name" in JSON to name in our struct.

✅ Summary (For Future You):
    • WeatherData → Struct type (template / blueprint)
    • decodedData → Instance created by decoding JSON
    • decodedData.name → Accessing the actual value decoded from the JSON

You can only access decoded values through the decoded instance (like decodedData).
You cannot access them directly through the struct type (like WeatherData.name) because that’s just a blueprint, not a real object.

✅ So your statement:

“Will this happen with the entire struct concept in Swift which handles only raw bytes?”

✅ Final Answer:

Yes. This process happens for every Swift struct that conforms to the Decodable protocol. Swift uses the struct’s property names to match against JSON keys, and it maps raw byte values directly into the struct’s properties.


## Q&A: Understanding JSON Decoding Internals in Swift

**Q:**  
Does the user-defined Swift struct store the raw JSON data internally during decoding, or where does that data reside if not inside the struct?

**A:**  
The raw JSON data initially resides in the `Data` object you provide to `JSONDecoder`. During decoding, the `JSONDecoder` internally parses and temporarily holds this raw data in its own buffers. After parsing, it converts the data into the appropriate Swift types and directly initializes your struct’s properties with those values.

The struct itself **does not** store the raw JSON data — it only contains the final decoded, strongly typed property values. So, the raw bytes are managed inside the decoder, **not** within your struct.


```swift

    func parseJSON(_ weatherData: Data) -> WeatherModel? {
        let decoder = JSONDecoder()
        do {
            let decodedData = try decoder.decode(WeatherData.self, from: weatherData)
            let id = decodedData.weather[0].id
            let temp = decodedData.main.temp
            let name = decodedData.name
            
            let weather = WeatherModel(conditionId: id, cityName: name, temperature: temp)
            return weather
        } catch {
            delegate?.didFailWithError(error: error)
            print("Failed to decode: \(error)")
            return nil
        }
    }
```
✅ What You Said (with your comment):
» Here it decoded it self using self and gets that value from the WeatherData from itself again which is decoded to        store here in separate var like id which will be plain text
🔍 What You Meant (and yes, you're right):
The decoder uses the structure of WeatherData (given by WeatherData.self) to understand how to decode the raw JSON.
Once decoded, you can access individual values from decodedData, like .id, .temp, and .name.
You're then assigning these to separate Swift variables like let id = ....
Why not?
The decode method requires two parameters:
```swift
func decode<T>(_ type: T.Type, from data: Data) throws -> T where T: Decodable
```
The first parameter is the type you want to decode into (e.g., WeatherData.self).
The second parameter is the raw JSON data (your Data object).

```swift
// 1. Create an instance of JSONDecoder, which will handle converting JSON data into Swift types
let decoder = JSONDecoder()

// 2. Use the decoder to decode the raw JSON data (weatherData)
//    - WeatherData.self tells the decoder: "Decode this JSON into an instance of the WeatherData struct"
//    - from: weatherData provides the raw JSON data to decode
// 3. The result is stored in decodedData, which is a fully populated WeatherData instance
let decodedData = try decoder.decode(WeatherData.self, from: weatherData)

```
