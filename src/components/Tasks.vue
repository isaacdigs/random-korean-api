<template>
    <div :key="task.id" v-for="task in tasks">
        <TaskComponent @delete-task="$emit('delete-task', task.id)" :task="task" />
    </div>
</template>

<script>
import TaskComponent from './Task'

    export default {
        name: 'TasksComponent',
        props: {
            tasks: Array,
        },
        components: {
            TaskComponent
        },
        emits: ['delete-task']
    }
</script>