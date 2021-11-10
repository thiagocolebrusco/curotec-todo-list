<template>
    <div>
        <div class="row">
            <div class="col col-md-12 text-left">
                <h1>Tasks ({{ numberOfTasks }})</h1>
            </div>
        </div>
        <div class="row">
            <div class="col col-md-12">
                <div class="input-group mb-3">
                    <input
                        type="text"
                        class="form-control"
                        v-model="task"
                        placeholder="New Task"
                    />
                    <div class="input-group-append">
                        <button
                            type="button"
                            class="btn btn-primary"
                            @click="add"
                        >
                            <span
                                >Add
                                <i class="bi bi-plus"></i>
                            </span>
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col col-md-12">
                <ul class="task-list">
                    <li
                        v-for="task in taskList"
                        :key="task.id"
                        :class="{ 'is-complete': task.is_complete }"
                    >
                        <div class="row">
                            <div
                                class="col col-md-11"
                                @click="changeStatus(task)"
                            >
                                <span class="description">{{
                                    task.description
                                }}</span>
                            </div>
                            <div class="col col-md-1">
                                <button
                                    type="button"
                                    class="btn btn-danger btn-block btn-remove"
                                    @click="remove(task)"
                                >
                                    <i class="bi bi-x"></i>
                                </button>
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            task: "",
            taskList: [],
        };
    },
    computed: {
        numberOfTasks() {
            return this.taskList?.filter((it) => !it.is_complete).length;
        },
    },
    created() {
        this.loadData();
    },
    methods: {
        async loadData() {
            const response = await axios("/api/tasks");
            if (response?.data) this.taskList = response.data;
        },
        async add() {
            if (!this.task) return; // TODO Add error message
            const description = this.task;

            const response = await axios.post("/api/tasks", { description });
            if (response?.data) this.loadData();
            this.task = "";
        },
        async changeStatus(item) {
            const is_complete = !item.is_complete;
            const response = await axios.put(`/api/tasks/${item.id}`, {
                is_complete,
            });
            this.loadData();
        },
        async remove(item) {
            const response = await axios.delete(`/api/tasks/${item.id}`);
            this.loadData();
        },
    },
};
</script>

<style scoped>
.task-list {
    display: block;
    padding-left: 0;
}
.task-list li {
    display: block;
    border: 1px black solid;
    width: 100%;
    list-style-type: none;
    background-color: #ebebeb;
    font-size: 20px;
    margin: 10px 0;
}
.task-list li.is-complete {
    background-color: #e5f5e1;
}
.task-list li .description {
    padding: 10px 20px;
    cursor: pointer;
}
.task-list li.is-complete .description {
    text-decoration: line-through;
}
.btn-remove {
    border-radius: 0;
}
</style>
