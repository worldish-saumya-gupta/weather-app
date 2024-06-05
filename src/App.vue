<template>
<h1>Weather App</h1>
<form @submit.prevent="handleSearch" class="form">
    <label class="city-name">Enter Your City</label>
    <div>
    <input type="text" v-model="city" required>
    <button type="submit" class="btn">Search</button>
</div>

</form>

<br>
<div v-if="showWeather">
    <h2>Daily Updates</h2>
    <div class="d-flex">
        <h4>Temp : {{temp}}</h4>
        <h4>Description: {{ description }}</h4>
        <h4>Pressure: {{ pressure }}</h4>
        <h4>Humidity: {{ humidity }}</h4>
        <h4>Wind: {{ wind }}</h4>
        <img :src="img" alt="">
    </div>
</div>
<div v-if="showWeather">
   <h2>Weather Forecast</h2>
    <div class="d-flex">
        <ul v-for="fc in forecast" :key="fc.weather[0].id">
            <h3>{{ formatDate(fc.dt) }} </h3>
            <img :src="`https://api.openweathermap.org/img/w/${fc.weather[0].icon}.png`" alt="">
            <p>{{ fc.main.temp }}</p>
        </ul>
    </div>
</div>

</template>

<script>
export default {
    components: {},
    data() {
        return {
            city: '',
            showWeather: false,
            temp: '',
            description: '',
            humidity: '',
            pressure: '',
            wind: '',
            img: null,
            forecast: []
        }
    },
    methods: {

        async handleSearch() {

            try {
                const result = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=65f8bec0641361e7105050e735d1022b`)
                if (!result.ok) {
                    throw new Error('City not found');
                }
                this.showWeather = true;
                const data = await result.json();
                this.temp = data.main.temp;
                this.description = data.weather[0].description;
                this.humidity = data.main.humidity;
                this.pressure = data.main.pressure;
                this.wind = data.wind.speed;
                this.showWeather = true;
                this.img = `https://api.openweathermap.org/img/w/${data.weather[0].icon}.png`
            } catch (error) {
                console.error(error);
            }

            try {
                const forcastResult = await fetch(`https://api.openweathermap.org/data/2.5/forecast?q=${this.city}&units=metric&appid=65f8bec0641361e7105050e735d1022b`)
                if (!forcastResult.ok) {
                    throw new Error('City not found');
                }
                const dataForcast = await forcastResult.json();
                const forecastList = dataForcast.list;
                
                const filterSameDateObjects = (list) => {
                const filteredList = [];
                const dateMap = {};

                list.forEach((item) => {
                    const date = item.dt_txt.split(' ')[0];
                    if (!dateMap[date]) {
                        filteredList.push(item);
                        dateMap[date] = true;
                    }
                });

                return filteredList;
            }
            const data = filterSameDateObjects(forecastList)
                for (let i = 0; i < data.length; i++) {
                    this.forecast.push(data[i])
                }
            } catch (error) {
                console.log(error)
            }

        },
        
            formatDate(time) {
                const newDate = new Date(time * 1000);
                return newDate.toLocaleDateString("en", {
                    weekday: "long"
                })
            }

    }
}
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #fff;
    background: #2c3e50;
    padding: 60px;
    height: 100vh;

}

.d-flex {
    display: flex;
    justify-content: space-around;
}

h1 {
    margin-top: 0;
}

form input {
    width: 40rem;
    padding: 1rem;
    border-radius: 1rem;
}

.btn{
    margin: 2rem;
    padding: 1rem;
    background: black;
    color: white;
    border-radius: 2rem;
}

.city-name{
    font-size: x-large;
    font-weight: 900;
}

.form{
    display: flex;
    flex-direction: column;
}
</style>
