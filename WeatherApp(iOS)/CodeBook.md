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
JSON

We pass the raw data to the parseJSON(weatherData:) function.
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
