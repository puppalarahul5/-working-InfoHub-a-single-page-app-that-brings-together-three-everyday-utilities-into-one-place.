import React, { useState, useEffect } from 'react';
import { Cloud, DollarSign, Sparkles, Loader2, AlertCircle, Sun, CloudRain, CloudSnow, Wind } from 'lucide-react';

const InfoHub = () => {
  const [activeTab, setActiveTab] = useState('weather');
  const [weather, setWeather] = useState(null);
  const [weatherCity, setWeatherCity] = useState('Mumbai');
  const [weatherLoading, setWeatherLoading] = useState(false);
  const [weatherError, setWeatherError] = useState(null);
  
  const [amount, setAmount] = useState('100');
  const [fromCurrency] = useState('INR');
  const [toCurrency, setToCurrency] = useState('USD');
  const [conversionResult, setConversionResult] = useState(null);
  const [conversionLoading, setConversionLoading] = useState(false);
  const [conversionError, setConversionError] = useState(null);
  
  const [quote, setQuote] = useState(null);
  const [quoteLoading, setQuoteLoading] = useState(false);
  const [quoteError, setQuoteError] = useState(null);

  const fetchWeather = async (city) => {
    setWeatherLoading(true);
    setWeatherError(null);
    try {
      const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=demo&units=metric`);
      if (!response.ok) {
        throw new Error('City not found or API error');
      }
      const data = await response.json();
      setWeather(data);
    } catch (err) {
      setWeatherError(err.message);
      // Mock data for demo purposes when API fails
      setWeather({
        name: city,
        main: { temp: 28, feels_like: 30, humidity: 65 },
        weather: [{ main: 'Clear', description: 'clear sky' }],
        wind: { speed: 3.5 }
      });
    } finally {
      setWeatherLoading(false);
    }
  };

  const convertCurrency = async () => {
    setConversionLoading(true);
    setConversionError(null);
    try {
      const amt = parseFloat(amount);
      if (isNaN(amt) || amt <= 0) {
        throw new Error('Please enter a valid amount');
      }
      
      // Mock exchange rates (in production, use real API like exchangerate-api.com)
      const rates = {
        'USD': 0.012,
        'EUR': 0.011
      };
      
      const convertedAmount = amt * rates[toCurrency];
      setConversionResult({
        from: amt,
        to: convertedAmount.toFixed(2),
        rate: rates[toCurrency]
      });
    } catch (err) {
      setConversionError(err.message);
    } finally {
      setConversionLoading(false);
    }
  };

  const fetchQuote = async () => {
    setQuoteLoading(true);
    setQuoteError(null);
    try {
      const response = await fetch('https://api.quotable.io/random');
      if (!response.ok) {
        throw new Error('Failed to fetch quote');
      }
      const data = await response.json();
      setQuote(data);
    } catch (err) {
      setQuoteError(err.message);
      // Mock quote for demo
      const mockQuotes = [
        { content: "The only way to do great work is to love what you do.", author: "Steve Jobs" },
        { content: "Success is not final, failure is not fatal: it is the courage to continue that counts.", author: "Winston Churchill" },
        { content: "Believe you can and you're halfway there.", author: "Theodore Roosevelt" }
      ];
      setQuote(mockQuotes[Math.floor(Math.random() * mockQuotes.length)]);
    } finally {
      setQuoteLoading(false);
    }
  };

  useEffect(() => {
    fetchWeather('Mumbai');
  }, []);

  const getWeatherIcon = (main) => {
    switch (main?.toLowerCase()) {
      case 'clear': return <Sun className="w-16 h-16 text-yellow-400" />;
      case 'rain': return <CloudRain className="w-16 h-16 text-blue-400" />;
      case 'snow': return <CloudSnow className="w-16 h-16 text-blue-200" />;
      default: return <Cloud className="w-16 h-16 text-gray-400" />;
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-4">
      <div className="max-w-4xl mx-auto">
        <header className="text-center mb-8 pt-8">
          <h1 className="text-4xl font-bold text-indigo-900 mb-2">InfoHub</h1>
          <p className="text-gray-600">Your everyday utilities in one place</p>
        </header>

        <div className="bg-white rounded-lg shadow-lg overflow-hidden">
          <div className="flex border-b">
            <button
              onClick={() => setActiveTab('weather')}
              className={`flex-1 py-4 px-6 font-medium transition-colors ${
                activeTab === 'weather'
                  ? 'bg-indigo-500 text-white'
                  : 'bg-gray-50 text-gray-700 hover:bg-gray-100'
              }`}
            >
              <Cloud className="inline-block w-5 h-5 mr-2" />
              Weather
            </button>
            <button
              onClick={() => setActiveTab('currency')}
              className={`flex-1 py-4 px-6 font-medium transition-colors ${
                activeTab === 'currency'
                  ? 'bg-indigo-500 text-white'
                  : 'bg-gray-50 text-gray-700 hover:bg-gray-100'
              }`}
            >
              <DollarSign className="inline-block w-5 h-5 mr-2" />
              Currency
            </button>
            <button
              onClick={() => setActiveTab('quotes')}
              className={`flex-1 py-4 px-6 font-medium transition-colors ${
                activeTab === 'quotes'
                  ? 'bg-indigo-500 text-white'
                  : 'bg-gray-50 text-gray-700 hover:bg-gray-100'
              }`}
            >
              <Sparkles className="inline-block w-5 h-5 mr-2" />
              Quotes
            </button>
          </div>

          <div className="p-8">
            {activeTab === 'weather' && (
              <div className="space-y-6">
                <h2 className="text-2xl font-bold text-gray-800">Weather Information</h2>
                
                <div className="flex gap-2">
                  <input
                    type="text"
                    value={weatherCity}
                    onChange={(e) => setWeatherCity(e.target.value)}
                    onKeyPress={(e) => e.key === 'Enter' && fetchWeather(weatherCity)}
                    placeholder="Enter city name"
                    className="flex-1 px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"
                  />
                  <button
                    onClick={() => fetchWeather(weatherCity)}
                    disabled={weatherLoading}
                    className="px-6 py-2 bg-indigo-500 text-white rounded-lg hover:bg-indigo-600 disabled:bg-gray-400 transition-colors"
                  >
                    {weatherLoading ? <Loader2 className="w-5 h-5 animate-spin" /> : 'Search'}
                  </button>
                </div>

                {weatherError && (
                  <div className="flex items-center gap-2 p-4 bg-yellow-50 border border-yellow-200 rounded-lg text-yellow-800">
                    <AlertCircle className="w-5 h-5" />
                    <span>Using demo data. {weatherError}</span>
                  </div>
                )}

                {weather && (
                  <div className="bg-gradient-to-br from-indigo-500 to-purple-600 text-white rounded-xl p-8">
                    <div className="text-center">
                      <h3 className="text-3xl font-bold mb-4">{weather.name}</h3>
                      <div className="flex justify-center mb-4">
                        {getWeatherIcon(weather.weather?.[0]?.main)}
                      </div>
                      <div className="text-5xl font-bold mb-2">{Math.round(weather.main?.temp)}°C</div>
                      <div className="text-xl mb-6 capitalize">{weather.weather?.[0]?.description}</div>
                      
                      <div className="grid grid-cols-3 gap-4 mt-6 pt-6 border-t border-white/20">
                        <div>
                          <div className="text-sm opacity-80">Feels Like</div>
                          <div className="text-xl font-semibold">{Math.round(weather.main?.feels_like)}°C</div>
                        </div>
                        <div>
                          <div className="text-sm opacity-80">Humidity</div>
                          <div className="text-xl font-semibold">{weather.main?.humidity}%</div>
                        </div>
                        <div>
                          <div className="text-sm opacity-80">Wind Speed</div>
                          <div className="text-xl font-semibold">{weather.wind?.speed} m/s</div>
                        </div>
                      </div>
                    </div>
                  </div>
                )}
              </div>
            )}

            {activeTab === 'currency' && (
              <div className="space-y-6">
                <h2 className="text-2xl font-bold text-gray-800">Currency Converter</h2>
                
                <div className="space-y-4">
                  <div>
                    <label className="block text-sm font-medium text-gray-700 mb-2">Amount (INR)</label>
                    <input
                      type="number"
                      value={amount}
                      onChange={(e) => setAmount(e.target.value)}
                      placeholder="Enter amount"
                      className="w-full px-4 py-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"
                    />
                  </div>

                  <div>
                    <label className="block text-sm font-medium text-gray-700 mb-2">Convert To</label>
                    <select
                      value={toCurrency}
                      onChange={(e) => setToCurrency(e.target.value)}
                      className="w-full px-4 py-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"
                    >
                      <option value="USD">USD (US Dollar)</option>
                      <option value="EUR">EUR (Euro)</option>
                    </select>
                  </div>

                  <button
                    onClick={convertCurrency}
                    disabled={conversionLoading}
                    className="w-full px-6 py-3 bg-indigo-500 text-white rounded-lg hover:bg-indigo-600 disabled:bg-gray-400 transition-colors font-medium"
                  >
                    {conversionLoading ? <Loader2 className="w-5 h-5 animate-spin mx-auto" /> : 'Convert'}
                  </button>
                </div>

                {conversionError && (
                  <div className="flex items-center gap-2 p-4 bg-red-50 border border-red-200 rounded-lg text-red-800">
                    <AlertCircle className="w-5 h-5" />
                    <span>{conversionError}</span>
                  </div>
                )}

                {conversionResult && (
                  <div className="bg-gradient-to-r from-green-500 to-emerald-600 text-white rounded-xl p-6">
                    <div className="text-center">
                      <div className="text-lg mb-2">Converted Amount</div>
                      <div className="text-4xl font-bold mb-4">
                        {conversionResult.to} {toCurrency}
                      </div>
                      <div className="text-sm opacity-90">
                        {conversionResult.from} INR = {conversionResult.to} {toCurrency}
                      </div>
                      <div className="text-xs opacity-75 mt-2">
                        Exchange Rate: 1 INR = {conversionResult.rate} {toCurrency}
                      </div>
                    </div>
                  </div>
                )}
              </div>
            )}

            {activeTab === 'quotes' && (
              <div className="space-y-6">
                <h2 className="text-2xl font-bold text-gray-800">Motivational Quotes</h2>
                
                <button
                  onClick={fetchQuote}
                  disabled={quoteLoading}
                  className="w-full px-6 py-3 bg-indigo-500 text-white rounded-lg hover:bg-indigo-600 disabled:bg-gray-400 transition-colors font-medium"
                >
                  {quoteLoading ? (
                    <Loader2 className="w-5 h-5 animate-spin mx-auto" />
                  ) : (
                    'Get New Quote'
                  )}
                </button>

                {quoteError && (
                  <div className="flex items-center gap-2 p-4 bg-yellow-50 border border-yellow-200 rounded-lg text-yellow-800">
                    <AlertCircle className="w-5 h-5" />
                    <span>Using demo quote. {quoteError}</span>
                  </div>
                )}

                {quote && (
                  <div className="bg-gradient-to-br from-purple-500 to-pink-600 text-white rounded-xl p-8">
                    <Sparkles className="w-12 h-12 mb-4 mx-auto" />
                    <blockquote className="text-2xl font-medium text-center mb-4 leading-relaxed">
                      "{quote.content}"
                    </blockquote>
                    <p className="text-center text-lg opacity-90">— {quote.author}</p>
                  </div>
                )}

                {!quote && !quoteLoading && (
                  <div className="text-center text-gray-500 py-12">
                    <Sparkles className="w-16 h-16 mx-auto mb-4 opacity-50" />
                    <p>Click the button above to get inspired!</p>
                  </div>
                )}
              </div>
            )}
          </div>
        </div>

        <footer className="text-center mt-8 text-gray-600 pb-8">
          <p>Built for ByteXL Coding Challenge</p>
        </footer>
      </div>
    </div>
  );
};

export default InfoHub;
