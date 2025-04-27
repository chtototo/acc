<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import moment from "moment";

const START_TIME = "Mon May 1 2025 12:00:00 GMT+0000";
const showForm = ref(false);
const showPrivacyPolicy = ref(false);
const firstOpen = ref(true);
const animateOnce = ref(false);

function getWordForm(number, forms) {
  number = Math.abs(number) % 100;
  const lastDigit = number % 10;
  if (number > 10 && number < 20) return forms[2];
  if (lastDigit > 1 && lastDigit < 5) return forms[1];
  if (lastDigit === 1) return forms[0];
  return forms[2];
}

const days = ref(0),
  hours = ref("00"),
  minutes = ref("00"),
  seconds = ref("00");
const dayLabel = ref("дней"),
  hourLabel = ref("часов"),
  minuteLabel = ref("минут"),
  secondLabel = ref("секунд");

function disableBodyScroll() {
  document.body.style.overflow = "hidden";
}

function enableBodyScroll() {
  document.body.style.overflow = "";
}

function openForm() {
  showForm.value = true;
  disableBodyScroll();
  if (firstOpen.value) {
    animateOnce.value = true;
    firstOpen.value = false;
  } else {
    animateOnce.value = false;
  }
}

function closeForm() {
  showForm.value = false;
  enableBodyScroll();
}

function openPrivacyPolicy() {
  showPrivacyPolicy.value = true;
}

function closePrivacyPolicy() {
  showPrivacyPolicy.value = false;
}

function backdropClickPrivacyPolicy() {
  showPrivacyPolicy.value = false;
}

let intervalId;

onMounted(() => {
  const endDate = moment(START_TIME);

  const updateTime = () => {
    const diff = moment.duration(endDate.diff(moment()));
    const d = Math.trunc(diff.asDays());
    const h = diff.hours();
    const m = diff.minutes();
    const s = diff.seconds();

    days.value = d;
    hours.value = h.toString().padStart(2, "0");
    minutes.value = m.toString().padStart(2, "0");
    seconds.value = s.toString().padStart(2, "0");

    dayLabel.value = getWordForm(d, ["день", "дня", "дней"]);
    hourLabel.value = getWordForm(h, ["час", "часа", "часов"]);
    minuteLabel.value = getWordForm(m, ["минута", "минуты", "минут"]);
    secondLabel.value = getWordForm(s, ["секунда", "секунды", "секунд"]);
  };

  updateTime();
  intervalId = setInterval(updateTime, 1000);
});

onUnmounted(() => {
  clearInterval(intervalId);
});
</script>

<template>
  <div
    class="min-h-screen w-full relative overflow-x-hidden"
    :class="showForm ? 'touch-none' : ''"
  >
    <div
      class="absolute inset-0 -z-10 bg-[url(./images/background.jpg)] bg-no-repeat bg-cover bg-[center_66%]"
    ></div>

    <div class="fixed inset-0 -z-10 bg-black/50"></div>

    <div
      class="text-white px-[5vw] sm:pt-28 max-sm:pt-8 flex flex-col items-center gap-4 max-sm:gap-16"
    >
      <div class="flex flex-col gap-2">
        <transition :name="animateOnce ? 'slide-down' : ''">
          <div
            v-if="showForm"
            class="fixed inset-0 bg-black/60 z-50 flex items-center justify-center px-4 font-light text-zinc-500"
          >
            <div
              class="bg-white rounded-xl shadow-xl max-w-md w-full pt-6 pb-12 px-16 max-sm:px-4 relative overflow-y-auto max-h-screen"
            >
              <button
                @click="closeForm"
                class="absolute top-2 right-3 text-3xl"
              >
                ×
              </button>

              <h2 class="text-md font-medium my-8 text-center">
                Отправь заявку на чемпионат прямо сейчас!
              </h2>

              <form class="flex flex-col gap-4">
                <div class="flex flex-col gap-1">
                  <label for="name" class="text-sm font-normal"
                    >ФИО <span>*</span></label
                  >
                  <input
                    id="name"
                    type="text"
                    class="border p-2 rounded-md focus:outline-[#5029de] h-10"
                    required
                  />
                </div>

                <div class="flex flex-col gap-1">
                  <label for="email" class="text-sm font-normal"
                    >Email <span>*</span></label
                  >
                  <input
                    id="email"
                    type="email"
                    class="border p-2 rounded-md focus:outline-[#5029de] h-10"
                    required
                  />
                </div>

                <div class="flex flex-col gap-1">
                  <label for="phone" class="text-sm font-normal"
                    >Телефон <span>*</span></label
                  >
                  <input
                    id="phone"
                    type="tel"
                    class="border p-2 rounded-md focus:outline-[#5029de] h-10"
                    required
                  />
                </div>

                <div class="flex flex-col gap-1">
                  <label for="team" class="text-sm font-normal"
                    >Организация / Команда <span>*</span></label
                  >
                  <input
                    id="team"
                    type="text"
                    class="border p-2 rounded-md focus:outline-[#5029de] h-10"
                    required
                  />
                </div>

                <div class="flex flex-col gap-1">
                  <label for="weight" class="text-sm font-normal">
                    Вес <span>*</span>
                  </label>
                  <select
                    id="weight"
                    class="border p-2 rounded-md focus:outline-[#5029de] bg-white h-10"
                  >
                    <option value="40">40</option>
                    <option value="41">41</option>
                    <option value="42">42</option>
                  </select>
                </div>

                <button
                  type="submit"
                  class="mt-2 bg-[#5029de] text-white py-2 rounded-md hover:bg-[#4124ab] transition shadow-[0_5px_5px_#00000050]"
                >
                  Зарегистрироваться
                </button>
                <p class="text-[.65em] text-center text-zinc-600">
                  Нажимая кнопку, вы даете согласие на
                  <button @click="openPrivacyPolicy" class="underline">
                    обработку персональных данных
                  </button>
                </p>
              </form>
            </div>
          </div>
        </transition>

        <transition name="fade">
          <div
            v-if="showPrivacyPolicy"
            class="fixed inset-0 z-50 flex items-center justify-center px-4 font-light text-zinc-500"
          >
            <div
              class="fixed inset-0 bg-black/50 z-40"
              @click="showPrivacyPolicy = false"
            ></div>

            <div
              class="relative bg-white rounded-xl shadow-xl max-w-lg w-full max-h-[80vh] p-8 z-50 overflow-y-auto"
            >
              <button
                @click="showPrivacyPolicy = false"
                class="absolute top-2 right-3 text-3xl cursor-pointer"
              >
                ×
              </button>
              <h3 class="text-xl font-semibold text-center mb-6">
                Политика конфиденциальности
              </h3>
              <p class="mb-4">
                Ваши персональные данные будут использоваться исключительно для
                целей обработки вашей заявки. Мы уважаем вашу конфиденциальность
                и не передаем информацию третьим лицам.
              </p>
              <p class="mb-4">
                Согласие на обработку персональных данных является обязательным
                для продолжения использования формы. Вы вправе в любой момент
                отозвать свое согласие.
              </p>
              <p class="mb-4">
                Мы принимаем меры по защите ваших данных в соответствии с
                действующим законодательством.
              </p>
              <p class="mb-4">
                Ознакомьтесь с полной версией нашей политики на сайте или
                свяжитесь с нами для получения дополнительной информации.
              </p>
            </div>
          </div>
        </transition>

        <h1
          class="text-[clamp(3rem,5vw,4rem)] border-2 border-[#5029de] bg-black/50 uppercase px-[2vw] py-[2vh] text-stroke text-center max-sm:text-[1rem] max-sm:px-[8vw]"
        >
          alliance championship 2025
        </h1>
        <div
          class="text-center font-medium text-[clamp(1rem,2vw,1.5rem)] max-sm:text-[.8rem] max-sm:opacity-[.9]"
        >
          <h2>Махачкала, Дворец спорта "Автодор"</h2>
          <h2>24-25 Мая, 2025</h2>
        </div>
      </div>

      <nav
        class="grid grid-cols-2 gap-1 px-[10vw] py-4 bg-black/50 uppercase text-center text-[clamp(0.9rem,1.2vw,1.1rem)] max-sm:hidden"
      >
        <a class="bg-[#5029de] p-2 font-medium" href="">Расписание турнира</a>
        <a class="bg-[#5029de] p-2 font-medium" href="">Список участников</a>
        <a class="bg-[#5029de] p-2 font-medium" href="">Условия допуска</a>
        <a class="bg-[#5029de] p-2 font-medium" href="">Оплата взноса</a>
      </nav>

      <div class="flex flex-col gap-4 w-full items-center">
        <button
          @click="openForm"
          class="bg-gradient-to-r from-[#5029de] to-[#4124ab] text-[clamp(.9rem,1.5vw,1.25rem)] uppercase px-8 py-3 rounded-[5px] font-medium max-sm:mt-96 w-fit"
        >
          зарегистрироваться
        </button>

        <div class="flex flex-col items-center w-full mt-8">
          <p class="font-medium text-sm sm:hidden">До турнира осталось:</p>
          <div class="flex flex-row justify-between w-full px-[5vw]">
            <div class="flex flex-col text-center">
              <p class="text-[clamp(2rem,8vw,4rem)]">{{ days }}</p>
              <p class="text-[clamp(1rem,2vw,1.5rem)] font-normal">
                {{ dayLabel }}
              </p>
            </div>
            <p class="text-[clamp(2rem,8vw,6rem)]">:</p>
            <div class="flex flex-col text-center">
              <p class="text-[clamp(2rem,8vw,4rem)]">{{ hours }}</p>
              <p class="text-[clamp(1rem,2vw,1.5rem)] font-normal">
                {{ hourLabel }}
              </p>
            </div>
            <p class="text-[clamp(2rem,8vw,6rem)]">:</p>
            <div class="flex flex-col text-center">
              <p class="text-[clamp(2rem,8vw,4rem)]">{{ minutes }}</p>
              <p class="text-[clamp(1rem,2vw,1.5rem)] font-normal">
                {{ minuteLabel }}
              </p>
            </div>
            <p class="text-[clamp(2rem,8vw,6rem)]">:</p>
            <div class="flex flex-col text-center">
              <p class="text-[clamp(2rem,8vw,4rem)]">{{ seconds }}</p>
              <p class="text-[clamp(1rem,2vw,1.5rem)] font-normal">
                {{ secondLabel }}
              </p>
            </div>
          </div>
        </div>
      </div>

      <nav class="sm:hidden flex flex-col gap-2 w-full mt-6">
        <a
          class="border-[#5029de] border-2 rounded-md p-2 font-medium text-center active:scale-[.97] bg-black/30"
          href=""
          >Расписание турнира</a
        >
        <a
          class="border-[#5029de] border-2 rounded-md p-2 font-medium text-center active:scale-[.97] bg-black/30"
          href=""
          >Список участников</a
        >
        <a
          class="border-[#5029de] border-2 rounded-md p-2 font-medium text-center active:scale-[.97] bg-black/30"
          href=""
          >Условия допуска</a
        >
        <a
          class="border-[#5029de] border-2 rounded-md p-2 font-medium text-center active:scale-[.97] bg-black/30"
          href=""
          >Оплата взноса</a
        >
      </nav>

      <div
        class="flex flex-col gap-6 w-full items-center mt-10 sm:absolute bottom-0 mb-4"
      >
        <div class="flex flex-col gap-2 items-center">
          <p class="sm:hidden font-semibold">Наши спонсоры:</p>
          <div class="flex flex-row gap-8 items-center">
            <a href="">
              <img
                class="h-[clamp(4rem,10vw,4rem)]"
                src="./images/миркато.png"
                alt=""
              />
            </a>
            <a href="">
              <img
                class="h-[clamp(6rem,15vw,8rem)]"
                src="./images/дихард.png"
                alt=""
              />
            </a>
            <a href="">
              <img
                class="h-[clamp(4rem,10vw,4rem)]"
                src="./images/архитек.png"
                alt=""
              />
            </a>
          </div>
        </div>

        <div
          class="flex flex-col gap-2 text-[.7rem] font-medium underline text-center sm:hidden"
        >
          <a href="">Согласие на обработку персональных данных</a>
          <a href="">Политика конфиденциальности</a>
        </div>
      </div>
    </div>
  </div>
</template>
