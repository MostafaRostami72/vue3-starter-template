<template>
    <header>
        <nav>
            <RouterLink :to="{ name: 'Dashboard' }">
                {{ $t('Dashboard') }}
            </RouterLink>
            |
            <RouterLink :to="{ name: 'Login' }">
                {{ $t('Login') }}
            </RouterLink>
        </nav>
    </header>

    <main>
        <h1>{{ $t('Login') }}</h1>

        <div >
            <label for="date-input">Select a date:</label>
            <date-picker id="date-input" :value="selectedDate" @input="updateSelectedDate" />
        </div>
    </main>
</template>


<script>
import DatePicker from './../components/datePicker/VDatePicker.vue'

export default {
    name: 'App',
    components: {
        DatePicker
    },
    data() {
        return {
            selectedDate: new Date(),
        }
    },
    methods: {
        updateSelectedDate(newDate) {
            this.selectedDate = newDate
        }
    }
}
</script>
