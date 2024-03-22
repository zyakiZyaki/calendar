<template>
    <div class="head">
    <div
    v-for="item in lang" :key="item"
    @click="changeLanguage(item)"
    >{{ item }}</div>
</div>
    <table>
        <thead>
            <tr>
                <th colspan="7">
                    {{ monthsNames[newDate.month] }} {{ newDate.year }}
                </th>
            </tr>

            <tr>
                <th v-for="name in daysNames" :key="name">
                    {{ name }}
                </th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="key in calendarRows.keys()" :key="key">
                <td v-for="(day, index) in calendarRows[key]" :key="index" @click="clickCell(day)">
                    {{ day }}
                </td>
            </tr>
        </tbody>
    </table>

    <input v-model="date" type="date" @keydown.enter="changeDate()">

</template>


<script>

export default {
    props: {
    propDate: {
      type: String,
      default: ''
    },
},

    data() {
        return {
            date: '',
            newDate: '',
            daysNames: [],
            monthsNames: [],
            calendarRows: [],
            lang: ['eng', 'rus']
        }
    },

    created() {

        //Проверяем пришел ли пропс, если нет берем сегодняшний день
        this.propDate ? this.date = this.propDate : this.today()

        //Ставим английский по умолчанию
        this.changeLanguage('eng')

        //Записываем нормализированные данные
        this.changeDate()

        //Обнуляем таблицу
        this.createNullRows()

        //Заполняем таблицу
        this.fillTable()
    },

    methods: {

        today() {

            //Нормализуем формат данных сегодняшнего дня под инпут и записываем в this.date
            const currentMonth=('0'+(new Date().getMonth()+1)).slice(-2)
            this.date = [new Date().getFullYear(),currentMonth,new Date().getDate()].join('-')
        },

        createNullRows() {
            //Обнуление таблицы
            this.calendarRows = [[], [], [], [], [], []]
        },

        clickCell(day) {
            if(day === '') {
                return
            }

            //Снова нормализуем под инпут, конечно это стоит вынести и переиспользовать
            const currentMonth=('0'+(this.month+1)).slice(-2)
            const currentDay=('0'+day).slice(-2)

            this.date = [this.year, currentMonth, currentDay].join('-')
        },

        changeLanguage(lang) {
            if(lang === 'eng') {
                this.daysNames = ['sun', 'mon', 'tue', 'wed', 'thu', 'fri', 'sat']
                this.monthsNames = [ "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December" ]
            }
            if(lang === 'rus') {
                this.daysNames = ['вск', 'пон', 'вт', 'ср', 'чт', 'пт', 'сб']
                this.monthsNames = [ "Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь" ]
            }
        },
        changeDate() {

            this.createNullRows()

            this.newDate = {
                day: this.normalizeDate.getDay(),
                month: this.month,
                year: this.year
            }

            this.fillTable()
        },

        fillTable() {

            //Здесь расчет таблицы, конечно, он требует оптимизации,
            //так как много переиспользуемого кода.
    
            let count = 0
            for (let i = 0; i < 7; i++) {
                if (i < this.firstDayOnMonth) {
                    this.calendarRows[0].push('')
                }
                else {
                    count++
                    this.calendarRows[0].push(count)
                }
            }
            for (let i = 0; i < 7; i++) {
                count++
                this.calendarRows[1].push(count)
            }
            for (let i = 0; i < 7; i++) {
                count++
                this.calendarRows[2].push(count)
            }
            for (let i = 0; i < 7; i++) {
                count++
                this.calendarRows[3].push(count)
            }
            for (let i = 0; i < 7; i++) {
                if (count === this.daysOnMonth) {
                    break
                }
                count++
                this.calendarRows[4].push(count)
            }
            while (count < this.daysOnMonth) {
                count++
                this.calendarRows[5].push(count)
            }
            if(this.calendarRows[5].length===0) {
                this.calendarRows.pop()
            }
        }
    },

    computed: {
        normalizeDate() {
            return new Date(this.date)
        },
        daysOnMonth() {
            return new Date(this.year, this.month + 1, 0).getDate()
        },
        firstDayOnMonth() {
            return new Date(this.year, this.month, 1).getDay()
        },
        year() {
            return this.normalizeDate.getFullYear()
        },
        month() {
            return this.normalizeDate.getMonth()
        }
    },

    watch: {
    }
}

</script>
