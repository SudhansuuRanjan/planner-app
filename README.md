# Planner App

[Live Site URL](https://spectre-7-planner-app.vercel.app/)

## Description

This is a simple planner app that allows you to add custom widgets to your dashboard.

## Built with

- ReactJs

## Which widget I am trying to add and why?

### Weather widget : 
I am trying to add a `weather widget` because it is a very useful widget to have on your dashboard. It allows you to see the weather forecast of your current location. It is very useful for planning your day.

## My Work and How It works?

- Added a `WeatherWidget` component in the `widgets` folder.
- Added the `WeatherWidget` component in the `WidgetGalleryModal`.
- Added a `.env` file to store the OpenWeatherMap API key.

### WeatherWidget Component

1. Used the `useState` hook to store the weather data, time, locationAllowed, error and loading state.

```jsx
    const [weather, setWeather] = useState(null);
    const [time, setTime] = useState(new Date().toLocaleTimeString());
    const [locationAllowed, setLocationAllowed] = useState(true);
    const [error, setError] = useState(null);
    const [loading, setLoading] = useState(false);
```

2. Used the `useEffect` hook to get the current time and update it every second.

```jsx
    useEffect(() => {
        // Update the time every second
        const interval = setInterval(() => {
            setTime(new Date().toLocaleTimeString())
        }, 1000);
        // Clean up the interval on component unmount
        return () => clearInterval(interval);
    }, [time]);
```
3.  Used the `useEffect` hook to get the current location If the location is availabe then with the help of `getWeatherData` function we are fetching the weather data from the OpenWeatherMap API, else we are setting the `locationAllowed` state to false.

    In the `getWeatherData` function we are fetching the weather data from the OpenWeatherMap API and setting the `weather` state to the fetched data. If there is any error then we are setting the `error` state to the error message.

```jsx
    async function getWeatherData(position) {
        // get the latitude and longitude from the geolocation API
        const latitude = position.coords.latitude;
        const longitude = position.coords.longitude;

        // fetch the weather data from the OpenWeatherMap API
        try {
            const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=${process.env.REACT_APP_WEATHER_API_KEY}`);
            const data = await response.json();
            setWeather(data);
            setLoading(false);
        } catch (error) {
            console.error(error.message);
            setError(error.message);
            setLoading(false);
        }
    }

    useEffect(() => {
        if (navigator.geolocation) {
            setLocationAllowed(true);
            // get the current position
            navigator.geolocation.getCurrentPosition(getWeatherData, (err) => {
                setLocationAllowed(false);
                setLoading(false);
            });
        } else {
            setLocationAllowed(false);
            console.error("Geolocation is not supported by this browser.");
        }
    }, []);
```

4. Handling `Location Not Availabe` , `Loading` and `Error` states.

```jsx
    // if location is not allowed, show a message
    if (!locationAllowed) {
        return (
            <div>
                <div style={styles.weathercontainer}>
                    <h1 style={{ fontSize: "1.3rem", padding: "1rem 0.5rem", fontWeight: "600", textAlign: "center" }}>Location is not allowed!</h1>
                    <p style={{
                        fontSize: "1rem",
                        padding: "0 0.5rem 1rem",
                        fontWeight: "500",
                        textAlign: "center",
                    }}>Allow access to Location to access weather info.</p>
                </div>
            </div>
        )
    }

    // if the weather data is not loaded, show a loading message
    if (loading) {
        return (
            <div>
                <div style={styles.weathercontainer}>
                    <h1 style={{ fontSize: "1.3rem", padding: "1rem 0.5rem", fontWeight: "600", textAlign: "center" }}>Loading...</h1>
                </div>
            </div>
        )
    }

    // if there is an error, show an error message
    if (error) {
        return (
            <div>
                <div style={styles.weathercontainer}>
                    <h1 style={{ fontSize: "1.3rem", padding: "1rem 0.5rem", fontWeight: "600", textAlign: "center" }}>Error!</h1>
                    <p style={{
                        fontSize: "1rem",
                        padding: "0 0.5rem 1rem",
                        fontWeight: "500",
                        textAlign: "center",
                    }}>{error}</p>
                </div>
            </div>
        )
    }
```

5. Rendering the Componet UI if everything goes well.

```jsx
    return (
        <div>
            {weather &&
                <div style={styles.weathercontainer}>
                    <div style={styles.container}>
                        <div>
                            <div style={styles.leftTop}>
                                <img alt="icon" style={styles.icon} src={`https://openweathermap.org/img/wn/${weather.weather[0].icon}@2x.png`} />
                                <div>{weather.weather[0].main}</div>
                            </div>
                            <div style={styles.currentTemp}>{(weather.main.temp - 273.13).toFixed(0)}° C</div>
                            <div>
                                Real Feel : {(weather.main.feels_like - 273.13).toFixed(0)}° C
                            </div>
                            <div>
                                Humidity : {weather.main.humidity}%
                            </div>
                            <div>
                                Wind Speed : {weather.wind.speed} km/h
                            </div>
                            <div>
                                Visibility : {weather.visibility / 1000} km
                            </div>
                            <div>
                                Pressure : {weather.main.pressure} hPa
                            </div>
                        </div>
                        <div style={styles.right}>
                            <div style={styles.dateTime}>
                                <div>{time}</div>
                                <div>{tidyDate(new Date())}</div>
                                <div style={styles.day}>{getDayAbbreviation()}</div>
                            </div>

                            <div style={styles.dateTime}>
                                <div style={styles.city}>{weather.name}</div>
                                <div>({formatTimeZone(weather.timezone)}) GMT</div>
                            </div>
                        </div>
                    </div>
                    <div style={styles.footer}>
                        <div>
                            Sunrise : {new Date(weather.sys.sunrise * 1000).toLocaleTimeString()}
                        </div>
                        <div>
                            Sunset : {new Date(weather.sys.sunset * 1000).toLocaleTimeString()}
                        </div>
                    </div>
                </div>
            }
        </div>
    )
```
6. For Styling, I have used Inline CSS.

```jsx
const styles = {
    icon: {
        height: "3rem",
        width: "3rem",
    },
    weathercontainer: {
        display: "flex",
        flexDirection: "column",
        width: "22rem"
    },
    container: {
        padding: "0.5rem",
        display: "flex",
        justifyContent: "space-between",
    },
    leftTop: {
        display: "flex",
        alignItems: "center",
        margin: "-0.6rem 0"
    },
    currentTemp: {
        fontSize: "3rem",
        fontWeight: "700",
    },
    right: {
        display: "flex",
        flexDirection: "column",
        justifyContent: "space-between",
        alignItems: "flex-end"
    },
    dateTime: {
        display: "flex",
        flexDirection: "column",
        alignItems: "flex-end"
    },
    day: {
        fontSize: "1.5rem",
    },
    city: {
        fontSize: "1.5rem",
        fontWeight: "600",
    },
    footer: {
        display: "flex",
        alignItems: "center",
        justifyContent: "space-between",
        padding: "0 0.5rem",
        marginTop: "-0.5rem",
        paddingBottom: "0.5rem",
    }
}
```

7. Helper Functions

```js
/**
 * Formats the timezone offset in seconds to a formatted string representation.
 * @param {number} offsetInSeconds - The timezone offset in seconds.
 * @returns {string} - The formatted timezone offset string, e.g., '+05:30'.
 */
function formatTimeZone(offsetInSeconds) {
    const offsetHours = Math.floor(Math.abs(offsetInSeconds) / 3600);
    const offsetMinutes = Math.floor((Math.abs(offsetInSeconds) % 3600) / 60);
    const sign = offsetInSeconds >= 0 ? '+' : '-';
    const formattedOffset = `${sign}${String(offsetHours).padStart(2, '0')}:${String(offsetMinutes).padStart(2, '0')}`;
    return formattedOffset;
}

/**
 * Converts the given date to a formatted date string.
 * @param {Date} date - The input date.
 * @returns {string} - The formatted date string, e.g., '1, January 2021'.
 */
const tidyDate = (date) => {
    const dateNumber = date.getDate();
    const month = date.getMonth() + 1;
    const year = date.getFullYear();
    const months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
    return `${dateNumber}, ${months[month - 1]} ${year}`;
}

/**
 * Returns the abbreviation of the current day of the week.
 * @returns {string} - The day abbreviation, e.g., 'SUN'.
 */
function getDayAbbreviation() {
    const date = new Date();
    const daysOfWeek = ['SUN', 'MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT'];
    const dayIndex = date.getDay();
    return daysOfWeek[dayIndex];
}
```

8. Finally, I have exported the component.

```jsx
export default WeatherWidget;
```

### Complete Code for the Weather Widget Component

```jsx
import { useState, useEffect } from 'react';

/**
 * Formats the timezone offset in seconds to a formatted string representation.
 * @param {number} offsetInSeconds - The timezone offset in seconds.
 * @returns {string} - The formatted timezone offset string, e.g., '+05:30'.
 */
function formatTimeZone(offsetInSeconds) {
    const offsetHours = Math.floor(Math.abs(offsetInSeconds) / 3600);
    const offsetMinutes = Math.floor((Math.abs(offsetInSeconds) % 3600) / 60);
    const sign = offsetInSeconds >= 0 ? '+' : '-';
    const formattedOffset = `${sign}${String(offsetHours).padStart(2, '0')}:${String(offsetMinutes).padStart(2, '0')}`;
    return formattedOffset;
}

/**
 * Converts the given date to a formatted date string.
 * @param {Date} date - The input date.
 * @returns {string} - The formatted date string, e.g., '1, January 2021'.
 */
const tidyDate = (date) => {
    const dateNumber = date.getDate();
    const month = date.getMonth() + 1;
    const year = date.getFullYear();
    const months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
    return `${dateNumber}, ${months[month - 1]} ${year}`;
}

/**
 * Returns the abbreviation of the current day of the week.
 * @returns {string} - The day abbreviation, e.g., 'SUN'.
 */
function getDayAbbreviation() {
    const date = new Date();
    const daysOfWeek = ['SUN', 'MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT'];
    const dayIndex = date.getDay();
    return daysOfWeek[dayIndex];
}

const WeatherWidget = () => {
    const [weather, setWeather] = useState(null);
    const [time, setTime] = useState(new Date().toLocaleTimeString());
    const [locationAllowed, setLocationAllowed] = useState(true);
    const [error, setError] = useState(null);
    const [loading, setLoading] = useState(false);

    useEffect(() => {
        // Update the time every second
        const interval = setInterval(() => {
            setTime(new Date().toLocaleTimeString())
        }, 1000);
        // Clean up the interval on component unmount
        return () => clearInterval(interval);
    }, [time]);

    async function getWeatherData(position) {
        // get the latitude and longitude from the geolocation API
        const latitude = position.coords.latitude;
        const longitude = position.coords.longitude;

        // fetch the weather data from the OpenWeatherMap API
        try {
            const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=${process.env.REACT_APP_WEATHER_API_KEY}`);
            const data = await response.json();
            setWeather(data);
            setLoading(false);
        } catch (error) {
            console.error(error.message);
            setError(error.message);
            setLoading(false);
        }
    }

    useEffect(() => {
        if (navigator.geolocation) {
            setLocationAllowed(true);
            // get the current position
            navigator.geolocation.getCurrentPosition(getWeatherData, (err) => {
                setLocationAllowed(false);
                setLoading(false);
            });
        } else {
            setLocationAllowed(false);
            console.error("Geolocation is not supported by this browser.");
        }
    }, []);

    // if location is not allowed, show a message
    if (!locationAllowed) {
        return (
            <div>
                <div style={styles.weathercontainer}>
                    <h1 style={{ fontSize: "1.3rem", padding: "1rem 0.5rem", fontWeight: "600", textAlign: "center" }}>Location is not allowed!</h1>
                    <p style={{
                        fontSize: "1rem",
                        padding: "0 0.5rem 1rem",
                        fontWeight: "500",
                        textAlign: "center",
                    }}>Allow access to Location to access weather info.</p>
                </div>
            </div>
        )
    }

    // if the weather data is not loaded, show a loading message
    if (loading) {
        return (
            <div>
                <div style={styles.weathercontainer}>
                    <h1 style={{ fontSize: "1.3rem", padding: "1rem 0.5rem", fontWeight: "600", textAlign: "center" }}>Loading...</h1>
                </div>
            </div>
        )
    }

    // if there is an error, show an error message
    if (error) {
        return (
            <div>
                <div style={styles.weathercontainer}>
                    <h1 style={{ fontSize: "1.3rem", padding: "1rem 0.5rem", fontWeight: "600", textAlign: "center" }}>Error!</h1>
                    <p style={{
                        fontSize: "1rem",
                        padding: "0 0.5rem 1rem",
                        fontWeight: "500",
                        textAlign: "center",
                    }}>{error}</p>
                </div>
            </div>
        )
    }

    return (
        <div>
            {weather &&
                <div style={styles.weathercontainer}>
                    <div style={styles.container}>
                        <div>
                            <div style={styles.leftTop}>
                                <img alt="icon" style={styles.icon} src={`https://openweathermap.org/img/wn/${weather.weather[0].icon}@2x.png`} />
                                <div>{weather.weather[0].main}</div>
                            </div>
                            <div style={styles.currentTemp}>{(weather.main.temp - 273.13).toFixed(0)}° C</div>
                            <div>
                                Real Feel : {(weather.main.feels_like - 273.13).toFixed(0)}° C
                            </div>
                            <div>
                                Humidity : {weather.main.humidity}%
                            </div>
                            <div>
                                Wind Speed : {weather.wind.speed} km/h
                            </div>
                            <div>
                                Visibility : {weather.visibility / 1000} km
                            </div>
                            <div>
                                Pressure : {weather.main.pressure} hPa
                            </div>
                        </div>
                        <div style={styles.right}>
                            <div style={styles.dateTime}>
                                <div>{time}</div>
                                <div>{tidyDate(new Date())}</div>
                                <div style={styles.day}>{getDayAbbreviation()}</div>
                            </div>

                            <div style={styles.dateTime}>
                                <div style={styles.city}>{weather.name}</div>
                                <div>({formatTimeZone(weather.timezone)}) GMT</div>
                            </div>
                        </div>
                    </div>
                    <div style={styles.footer}>
                        <div>
                            Sunrise : {new Date(weather.sys.sunrise * 1000).toLocaleTimeString()}
                        </div>
                        <div>
                            Sunset : {new Date(weather.sys.sunset * 1000).toLocaleTimeString()}
                        </div>
                    </div>
                </div>
            }
        </div>
    )
}

export default WeatherWidget;

const styles = {
    icon: {
        height: "3rem",
        width: "3rem",
    },
    weathercontainer: {
        display: "flex",
        flexDirection: "column",
        width: "22rem"
    },
    container: {
        padding: "0.5rem",
        display: "flex",
        justifyContent: "space-between",
    },
    leftTop: {
        display: "flex",
        alignItems: "center",
        margin: "-0.6rem 0"
    },
    currentTemp: {
        fontSize: "3rem",
        fontWeight: "700",
    },
    right: {
        display: "flex",
        flexDirection: "column",
        justifyContent: "space-between",
        alignItems: "flex-end"
    },
    dateTime: {
        display: "flex",
        flexDirection: "column",
        alignItems: "flex-end"
    },
    day: {
        fontSize: "1.5rem",
    },
    city: {
        fontSize: "1.5rem",
        fontWeight: "600",
    },
    footer: {
        display: "flex",
        alignItems: "center",
        justifyContent: "space-between",
        padding: "0 0.5rem",
        marginTop: "-0.5rem",
        paddingBottom: "0.5rem",
    }
}
```

### Working of the Widget

Open the widget gallery modal and click the `WeatherWidget` button. The widget will be added to the dashboard. The widget will show the weather data of the current location. If the location is not allowed, the widget will show a message. If the weather data is not loaded, the widget will show a loading message. If there is an error, the widget will show an error message.

![Weather Widget](https://i.ibb.co/TMrPNhx/Screenshot-1429-min.png)

That's it.We have successfully created a weather widget.
