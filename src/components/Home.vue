<script setup lang="ts">
import { computed, ref } from "vue"
import {
    startOfMonth,
    endOfMonth,
    startOfWeek,
    endOfWeek,
    addDays,
    format,
} from "date-fns"
import { Icon } from '@iconify/vue'

const currentDate = ref(new Date())
const selectedDate = ref(format(new Date(), "yyyy-MM-dd"))

const days = computed(() => {
    const start = startOfWeek(startOfMonth(currentDate.value))
    const end = endOfWeek(endOfMonth(currentDate.value))

    const result = []
    let day = start

    while (day <= end) {
        result.push(day)
        day = addDays(day, 1)
    }

    return result
})

function selectDay(day: Date) {
    selectedDate.value = format(day, "yyyy-MM-dd")
}


type Workout = {
    id: string
    type: "corrida" | "academia"
    description: string
    done: boolean
}

const workouts = ref<Workout[]>([])
const calories = ref(0)
const input = ref("")
const type = ref<"corrida" | "academia">("corrida")

function addWorkout() {
    if (!input.value) return

    workouts.value.push({
        id: crypto.randomUUID(),
        description: input.value,
        type: type.value,
        done: false,
    })

    input.value = ""
}

function toggleWorkout(id: string) {
    workouts.value = workouts.value.map((w) =>
        w.id === id ? { ...w, done: !w.done } : w
    )
}
</script>

<template>
    <div class="bg-zinc-900 text-white p-6 min-h-screen">

        <!-- HEADER -->
        <header class="mb-6 gap-5 flex flex-col">
            <h1 class="text-3xl font-bold">🏃 Crono Runner</h1>
            <p class="text-zinc-400">
                Organize seus treinos e acompanhe sua evolução
            </p>
        </header>
        <!-- DETALHE DO DIA -->
        <div class="bg-zinc-800 rounded-2xl p-4 shadow mb-6">
            <h2 class="text-xl font-semibold mb-2 grid grid-cols-[auto_1fr] items-center">
                <Icon icon="mdi:calendar" class="mr-2" />
                {{ selectedDate }}
            </h2>


            <!-- LISTA -->

            <div class="space-y-2">

                <div class="flex items-center justify-between bg-zinc-700 p-4 rounded-xl">

                    <!-- 🔹 BLOCO 1 -->
                    <div class="flex items-center gap-4 flex-1">
                        <Icon icon="fluent-emoji-flat:running-shoe" class="w-10 h-10" />

                        <div>
                            <div class="text-2xl font-bold">
                                4 KM
                            </div>
                            <div class="text-sm">
                                tempo • pace
                            </div>
                        </div>
                    </div>

                    <!-- DIVISOR -->
                    <div class="w-px h-10 bg-zinc-500 mx-4"></div>

                    <!-- 🔹 BLOCO 2 -->
                    <div class="flex items-center gap-2 flex-1 justify-center text-yellow-400">
                        <Icon icon="mdi:dumbbell" class="w-6 h-6" />
                        <span class="font-medium">treino</span>
                    </div>

                    <!-- DIVISOR -->
                    <div class="w-px h-10 bg-zinc-500 mx-4"></div>

                    <!-- 🔹 BLOCO 3 -->
                    <div class="flex items-center gap-2 flex-1 justify-end">
                        <Icon icon="mdi:fire" class="w-6 h-6" />
                        <span class="font-medium">{{ calories }} kcal</span>
                    </div>

                </div>
            </div>

        </div>


        <div class="bg-zinc-800 rounded-2xl p-4 ">
            <!-- HEADER -->
            <h2 class="text-xl font-semibold mb-4">
                {{ format(currentDate, "MMMM yyyy") }}
            </h2>

            <!-- DIAS DA SEMANA -->
            <div class="grid grid-cols-7 text-center text-sm text-zinc-400 mb-2">
                <span>Dom</span>
                <span>Seg</span>
                <span>Ter</span>
                <span>Qua</span>
                <span>Qui</span>
                <span>Sex</span>
                <span>Sáb</span>
            </div>

            <!-- GRID -->
            <div class="grid grid-cols-7 gap-2">
                <button v-for="day in days" :key="day.toString()" @click="selectDay(day)"
                    class="p-2 rounded-lg text-sm transition" :class="[
                        format(day, 'yyyy-MM-dd') === selectedDate
                            ? 'bg-green-600'
                            : 'bg-zinc-700 hover:bg-zinc-600',

                        format(day, 'MM') !== format(currentDate, 'MM')
                            ? 'opacity-40'
                            : ''
                    ]">
                    {{ format(day, "d") }}
                </button>
            </div>


        </div>
        <div class="bg-zinc-800 rounded-2xl p-4 shadow mt-6 ">
            <!-- CALORIAS -->
            <div class="mb-4">
                <label class="text-sm text-zinc-400">
                    Calorias do dia
                </label>
                <input type="number" v-model="calories" class="w-full mt-1 p-2 rounded-lg bg-zinc-700 outline-none" />
            </div>
            <!-- FORM -->
            <div class="flex gap-2 mb-4">
                <input v-model="input" placeholder="Ex: corrida 5km"
                    class="flex-1 p-2 rounded-lg bg-zinc-700 outline-none" />

                <select v-model="type" class="bg-zinc-700 rounded-lg p-2">
                    <option value="corrida">Corrida</option>
                    <option value="academia">Academia</option>
                </select>

                <button @click="addWorkout" class="bg-green-600 hover:bg-green-500 px-4 rounded-lg">
                    +
                </button>
            </div>
        </div>
    </div>
</template>