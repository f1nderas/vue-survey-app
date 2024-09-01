<template>
  <v-app>
    <v-container>
      <v-row>
        <v-col cols="12">
          <h1>Получение опроса по дате</h1>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12" md="6">
          <v-text-field
            v-model="fromDate"
            label="Начальная дата"
            type="date"
            :rules="[dateRule]"
          >
          </v-text-field>
        </v-col>
        <v-col cols="12" md="6">
          <v-text-field
            v-model="toDate"
            label="Конечная дата"
            type="date"
            :rules="[dateRule]"
          >
          </v-text-field>
        </v-col>
      </v-row>

      <v-btn @click="fetchSurveys">Получить опросы</v-btn>

      <v-alert v-if="error" type="error">{{ error }}</v-alert>

      <v-row>
        <v-col v-for="survey in surveys" :key="survey.id" cols="12" md="4">
          <v-card>
            <v-card-title>{{ survey.order_id }}</v-card-title>
            <v-card-text>
              <p>Источник: {{ survey.source }}</p>
              <p>Оценка продукта: {{ survey.product_rating }}</p>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script lang="ts">
import { ref } from "vue";
import axios from "axios";

export default {
  setup() {
    const fromDate = ref<string>("");
    const toDate = ref<string>("");
    const surveys = ref<any[]>([]);
    const error = ref<string | null>(null);

    const dateRule = (value: string) => !!value || "Дата обязательна";

    const fetchSurveys = async () => {
      if (!fromDate.value || !toDate.value) {
        error.value = "Пожалуйста, выберите обе даты.";
        return;
      }

      try {
        const response = await axios.get(
          "https://bot.artis21.ru/api/get_surveys.php",
          {
            params: {
              from_date: fromDate.value,
              to_date: toDate.value,
            },
          }
        );
        surveys.value = response.data;
        error.value = null;
      } catch (err) {
        error.value = "Ошибка при получении данных";
      }
    };

    return { fromDate, toDate, surveys, error, fetchSurveys, dateRule };
  },
};
</script>
