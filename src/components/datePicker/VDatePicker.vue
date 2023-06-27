<template>
<div class="date-picker">
    <div class="date-picker-input-container">
        <input type="text" class="date-picker-input" :value="formattedValue" @focus="showPicker" @input="updateValue" />
        <div class="date-picker-icon" @click="showPicker">&#9660;</div>
    </div>
    <div v-if="isOpen" class="date-picker-dropdown">
        <div class="date-picker-header">
            <button @click="previousMonth">&lt;</button>
            <select v-model="selectedMonth" @change="changeMonth">
                <option v-for="(m, index) in months" :key="index" :value="index">{{ m }}</option>
            </select>

            <select v-model="selectedYear" @input="aaa">
                <option v-for="y in years" :key="y" :value="y">{{ y }}</option>
            </select>

            <button @click="nextMonth">&gt;</button>
        </div>
        <div class="date-picker-body">
            <div class="date-picker-row">
                <div class="date-picker-cell">Sun</div>
                <div class="date-picker-cell">Mon</div>
                <div class="date-picker-cell">Tue</div>
                <div class="date-picker-cell">Wed</div>
                <div class="date-picker-cell">Thu</div>
                <div class="date-picker-cell">Fri</div>
                <div class="date-picker-cell">Sat</div>
            </div>
            <div v-for="week in weeks" :key="week">
                <div class="date-picker-row">
                    <div v-for="day in week" :key="day.date"
                         :class="[{ 'date-picker-cell': true, 'date-picker-cell-disabled': day.disabled }, { 'date-picker-cell-selected': isSelected(day) }]"
                         @click="selectDate(day)"
                    >
                        {{ day.date }}
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>

<script>
export default {
    name: 'DatePicker',
    props: {
        value: {
            type: Date,
            required: true
        },
    },
    data() {
        return {
            now: new Date(),
            selectedDate: this.value,
            isOpen: false,
            selectedYear: this.value.getFullYear(),
            selectedMonth: this.value.getMonth()
        }
    },
    computed: {
        year() {
            return this.selectedDate.getFullYear()
        },
        month() {
            return this.getMonthName(this.selectedDate.getMonth())
        },
        weeks() {
            const daysInMonth = this.getDaysInMonth(this.selectedDate.getMonth(), this.selectedDate.getFullYear())
            const firstDayOfMonth = new Date(this.selectedDate.getFullYear(), this.selectedDate.getMonth(), 1).getDay()
            const weeks = []
            let week = []
            for (let i = 0; i < firstDayOfMonth; i++) {
                week.push({ disabled: true })
            }
            for (let i = 1; i <= daysInMonth; i++) {
                const date = new Date(this.selectedDate.getFullYear(), this.selectedDate.getMonth(), i)
                week.push({
                    date: i,
                    disabled: this.isDisabled(date),
                })
                if (week.length === 7) {
                    weeks.push(week)
                    week = []
                }
            }
            if (week.length > 0) {
                while (week.length < 7) {
                    week.push({ disabled: true })
                }
                weeks.push(week)
            }
            return weeks
        },
        months() {
            return [
                'January',
                'February',
                'March',
                'April',
                'May',
                'June',
                'July',
                'August',
                'September',
                'October',
                'November',
                'December'
            ]
        },
        years() {
            const years = []
            for (let year = new Date(2000, 5, 1).getFullYear(); year <= this.now.getFullYear(); year++) {
                years.push(year)
            }
            return years
        },
        formattedValue() {
            return this.selectedDate.toLocaleDateString()
        }
    },
    methods: {
        aaa(e) {
            console.log('input', e.target.value);
        },
        getMonthName(monthIndex) {
            const months = [
                'January',
                'February',
                'March',
                'April',
                'May',
                'June',
                'July',
                'August',
                'September',
                'October',
                'November',
                'December'
            ]
            return months[monthIndex]
        },
        getDaysInMonth(monthIndex, year) {
            return new Date(year, monthIndex + 1, 0).getDate()
        },
        isDisabled(date) {
            if (this.minDate && date < this.minDate) {
                return true
            }
            if (this.maxDate && date > this.maxDate) {
                return true
            }
            return false
        },
        previousMonth() {
            const year = this.selectedDate.getFullYear()
            const month = this.selectedDate.getMonth() - 1

            this.selectedYear = year;
            this.selectedMonth = month;
            this.selectedDate = new Date(year, month, 1)
        },
        nextMonth() {
            const year = this.selectedDate.getFullYear()
            const month = this.selectedDate.getMonth() + 1

            this.selectedYear = year;
            this.selectedMonth = month;
            this.selectedDate = new Date(year, month, 1)
        },
        isSelected(day) {
            if (this.selectedDate) {
                return this.selectedDate.getDate() === day.date &&
                    this.selectedDate.getMonth() === this.selectedDate.getMonth() &&
                    this.selectedDate.getFullYear() === this.year
            }
            return false
        },
        selectDate(day) {
            if (!day.disabled) {
                const year = this.selectedDate.getFullYear()
                const month = this.selectedDate.getMonth()
                this.selectedDate = new Date(year, month, day.date)
                this.$emit('input', this.selectedDate)
            }
        },
        showPicker() {
            this.isOpen = true
        },
        updateValue(event) {
            const newDate = new Date(event.target.value)
            if (!isNaN(newDate.getTime())) {
                this.selectedDate = newDate
                this.$emit('input', this.selectedDate)
            }
        },
        changeYear(event) {
            //this.selectedYear =event.target.value;

            console.log('changeYear', event.target.value, this.selectedYear);
            this.selectedDate = new Date(this.selectedYear, this.selectedMonth, 1)
        },
        changeMonth() {
            this.selectedDate = new Date(this.selectedYear, this.selectedMonth, 1)
        },
    }
}
</script>

<style>
.date-picker {
    position: relative;
}

.date-picker-input-container {
    display: flex;
    align-items: center;
}

.date-picker-input {
    padding: 5px;
    font-size: 16px;
    border: 1px solid #ccc;
    border-radius: 3px;
}

.date-picker-icon {
    margin-left: 5px;
    font-size: 16px;
    cursor: pointer;
}

.date-picker-dropdown {
    position: absolute;
    top: 100%;
    left: 0;
    min-width: 200px;
    background-color: #fff;
    border: 1px solid #ccc;
    border-radius: 3px;
    z-index: 100;
}

.date-picker-header {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 5px;
}

.date-picker-header button {
    background-color: transparent;
    border: none;
    margin: 0 5px;
    font-size: 16px;
    cursor: pointer;
}

.date-picker-body {
    display: flex;
    flex-direction: column;
    padding: 5px;
}

.date-picker-row {
    display: flex;
}

.date-picker-cell {
    flex: 1;
    padding: 5px;
    text-align: center;
    cursor: pointer;
}

.date-picker-cell-disabled {
    color: #ccc;
    cursor: not-allowed;
}

.date-picker-cell-selected {
    background-color: #ccc;
    color: #fff;
}
</style>
