<template>
  <div>
    <div class="pt-3 pb-5">
      <div class="progress-tile pb-3 text-center">
        <h3>Progress</h3>
      </div>
      <b-container class="mb-3">
        <b-button-group>
          <nuxt-link to="/progress/surah">
            <b-button class="progress-tabs">Surah</b-button>
          </nuxt-link>
          <nuxt-link to="/progress/juz">
            <b-button class="progress-tabs-active">Juz</b-button>
          </nuxt-link>
        </b-button-group>
      </b-container>
      <b-container>
        <b-row class="mb-2">
          <b-col><h5 class="progress-header my-auto">Memorized</h5></b-col>
          <b-col>
            <h6 class="progress-header text-right my-auto">
              {{ memo_value }} / {{ max }}
            </h6>
          </b-col>
        </b-row>
        <b-progress
          :value="memo_value"
          :max="max"
          height="18px"
          class="memo-progress-bar"
        ></b-progress>
        <div class="mt-3 mb-4">
          <b-row class="mb-2">
            <b-col><h5 class="progress-header my-auto">Tested</h5></b-col>
            <b-col>
              <h6 class="progress-header text-right my-auto">
                {{ test_value }} / {{ max }}
              </h6>
            </b-col>
          </b-row>
          <b-progress
            :value="test_value"
            :max="max"
            height="18px"
            class="test-progress-bar"
          ></b-progress>
        </div>
        <hr />
        <b-list-group v-for="(juz, index) in juz_list" :key="juz.name">
          <b-list-group-item
            v-b-toggle="`collapse-surahs-${index}`"
            class="m-1 juz-list"
          >
            <b-row>
              <b-col>
                <img
                  src="~/assets/img/collapse.png"
                  alt=""
                  class="inline-block"
                  height="3"
                  width="5"
                />
                {{ juz.name }}
                <b-collapse :id="`collapse-surahs-${index}`" class="my-2">
                  <div
                    v-for="surah in juz.surahs"
                    :key="surah.name"
                    class="mb-2 ml-2"
                  >
                    <nuxt-link
                      :to="`/progress/surah/${surah.name}/${surah.first_ayat}`"
                      class="surah-list"
                    >
                      {{ surah.name }}
                      <b-badge variant="primary" pill>{{ surah.ayat }}</b-badge>
                    </nuxt-link>
                  </div>
                </b-collapse>
              </b-col>
              <b-col class="text-right">
                <b-button disabled class="btn-circle memo-circle">
                  {{ Math.round((juz.memo / juz.max) * 100 * 10) / 10 }}%
                </b-button>
                <b-button disabled class="btn-circle test-circle">
                  {{ Math.round((juz.test / juz.max) * 100 * 10) / 10 }}%
                </b-button>
              </b-col>
            </b-row>
          </b-list-group-item>
        </b-list-group>
      </b-container>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      memo_value: 0,
      test_value: 0,
      max: 30,
      juz_list: [
        {
          name: "Juz 1",
          max: 150,
          memo: 0,
          test: 0,
          surahs: [
            { name: "Al-Faatiha", ayat: "1-7", first_ayat: "1" },
            { name: "Al-Baqara", ayat: "1-141", first_ayat: "1" },
          ],
        },
        {
          name: "Juz 2",
          max: 111,
          memo: 0,
          test: 0,
          surahs: [{ name: "Al-Baqara", ayat: "142-252", first_ayat: "142" }],
        },
        {
          name: "Juz 3",
          max: 126,
          memo: 0,
          test: 0,
          surahs: [
            { name: "Al-Baqara", ayat: "253-286", first_ayat: "253" },
            { name: "Aal-i-Imraan", ayat: "1-92", first_ayat: "1" },
          ],
        },
        {
          name: "Juz 4",
          max: 131,
          memo: 0,
          test: 0,
          surahs: [
            { name: "Aal-i-Imraan", ayat: "93-200", first_ayat: "93" },
            { name: "An-Nisaa", ayat: "1-23", first_ayat: "1" },
          ],
        },
        {
          name: "Juz 5",
          max: 124,
          memo: 0,
          test: 0,
          surahs: [{ name: "An-Nisaa", ayat: "24-147", first_ayat: "24" }],
        },
        {
          name: "Juz 6",
          max: 110,
          memo: 0,
          test: 0,
          surahs: [
            { name: "An-Nisaa", ayat: "148-176", first_ayat: "148" },
            { name: "Al-Maaida", ayat: "1-81", first_ayat: "1" },
          ],
        },
        {
          name: "Juz 7",
          max: 149,
          memo: 0,
          test: 0,
          surahs: [
            { name: "Al-Maaida", ayat: "82-120", first_ayat: "82" },
            { name: "Al-An'aam", ayat: "1-110", first_ayat: "1" },
          ],
        },
      ],
    };
  },
};
</script>

<style>
.badge-primary {
  background-color: #aaaaaa;
  font-weight: 400;
}
</style>
