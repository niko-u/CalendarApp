<script setup>
import { ref, reactive, watch } from 'vue'
import '@fullcalendar/core/vdom'
import FullCalendar from '@fullcalendar/vue3'
import dayGridPlugin from '@fullcalendar/daygrid'
import timeGridPlugin from '@fullcalendar/timegrid'
import listPlugin from '@fullcalendar/list'
import interactionPlugin from '@fullcalendar/interaction'
import useEvents from '../composables/useEvents.js'

const id = ref(10)

const { getEvents , createEvent, updateEvent, deleteEvent } = useEvents()

const options = reactive({
    plugins: [dayGridPlugin, timeGridPlugin, listPlugin, interactionPlugin],
    initialView: 'dayGridMonth',
    headerToolbar: {
        left: 'prev, today, next',
        center: 'title',
        right: 'dayGridMonth, dayGridWeek, dayGridDay listDay'
    },
    editable: true,
    selectable: true,
    weekends: true,
    select: (arg) => {
        id.value = id.value + 1

        const cal = arg.view.calendar
        cal.unselect()
        cal.addEvent({
            id: `${id.value}`,
            title: `New Event ${id.value}`,
            start: arg.start,
            end: arg.end,
            allDay: true
        })
    },
    eventClick: (arg) => {
        if (arg.event) {
            arg.event.remove()
        }
    },
    events: [

    ],
    eventAdd: (arg) => {
        createEvent({
            id: arg.event.id,
            title: arg.event.title,
            start: arg.event.start,
            end: arg.event.end,
            allDay: arg.event.allDay
        })
    },
    eventChange: (arg) => {
        updateEvent({
            id: arg.event.id,
            title: arg.event.title,
            start: arg.event.start,
            end: arg.event.end,
            allDay: arg.event.allDay
        }, arg.event.id)
    },
    eventRemove: (arg) => {
        deleteEvent(arg.event.id)
    }
})


options.events = getEvents.value
watch(getEvents, () => {
   options.events = getEvents.value
})
</script>

<template>
    <input type="file" ref="myFile" @change="selectedFile"><br/>
    <FullCalendar v-bind:options="options" />
</template>

<script>
    import  { ref } from 'vue'
    let data = []

    export default {
    methods: {
        selectedFile() {
          //  console.log('selected a file');
           // console.log(this.$refs.myFile.files[0]);
            
            let file = this.$refs.myFile.files[0];
           // if(!file || file.type !== 'text/plain') return;
            
            // Credit: https://stackoverflow.com/a/754398/52160
            let reader = new FileReader();
            reader.onload = function(e) {
                var content = e.target.result;
                var intern = JSON.parse(content); // parse json 
                console.log(intern); // You can index every object
            };
            data = reader.readAsText(file);
            // cal.addEvent({
            //     id: "1",
            //     title: "new event",
            //     start: "data",
            //     end: "end",
            //     allDay: true
            // })
            
        }
    }
    };
</script>
