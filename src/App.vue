<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref } from 'vue'
import framesData from './data/frames.json'

type Frame = {
  id: string
  name: string
  price: number
  size: string
  wood: string
  finish: string
  glass: string
  description: string
  features: string[]
  availability: string
  profile: string
  depth: string
  weight: string
  tone: string
  palette: string[]
  care: string
}

const frames = framesData as Frame[]

const hash = ref(window.location.hash || '#/')
const onHashChange = () => {
  hash.value = window.location.hash || '#/'
}

onMounted(() => window.addEventListener('hashchange', onHashChange))
onUnmounted(() => window.removeEventListener('hashchange', onHashChange))

const route = computed(() => {
  const value = hash.value.replace('#', '')
  return value || '/'
})

const section = computed<'home' | 'details' | 'contacts'>(() => {
  if (route.value.startsWith('/contacts')) return 'contacts'
  if (route.value.startsWith('/frames/')) return 'details'
  return 'home'
})

const frameId = computed(() => {
  if (!route.value.startsWith('/frames/')) return ''
  const parts = route.value.split('/')
  return parts[2] || ''
})

const currentFrame = computed(() => frames.find((frame) => frame.id === frameId.value) || null)
const featuredFrames = computed(() => frames.slice(0, 3))

const priceFormatter = new Intl.NumberFormat('ru-RU')
const formatPrice = (price: number) => `${priceFormatter.format(price)} ₽`
</script>

<template>
  <div class="page">
    <header class="site-header">
      <div class="container header-inner">
        <a class="logo" href="#/">
          <span class="logo-mark"></span>
          <span class="logo-text">Framewood</span>
        </a>
        <nav class="nav">
          <a class="nav-link" :class="{ active: section === 'home' }" href="#/">Каталог</a>
          <a class="nav-link" :class="{ active: section === 'contacts' }" href="#/contacts">Контакты</a>
        </nav>
        <a class="cta" href="#/contacts">Оформить заказ</a>
      </div>
    </header>

    <main>
      <section v-if="section === 'home'" class="hero">
        <div class="container hero-grid">
          <div class="hero-copy">
            <p class="eyebrow">Мастерская деревянных рамок</p>
            <h1>Теплая минималистичная коллекция для дома и галереи</h1>
            <p class="lead">
              Рамки из натурального дерева с деликатной геометрией и спокойной палитрой.
              Каждая модель доступна в нескольких оттенках, с антибликовым стеклом и ровной подгонкой.
            </p>
            <div class="hero-actions">
              <a class="button primary" href="#catalog">Смотреть каталог</a>
              <a class="button ghost" href="#/contacts">Уточнить заказ</a>
            </div>
            <div class="hero-meta">
              <div>
                <span class="meta-title">Производство</span>
                <span class="meta-value">Москва · 7 дней</span>
              </div>
              <div>
                <span class="meta-title">Материалы</span>
                <span class="meta-value">дуб · ясень · орех</span>
              </div>
              <div>
                <span class="meta-title">Сервис</span>
                <span class="meta-value">подбор и монтаж</span>
              </div>
            </div>
          </div>
          <div class="hero-visual">
            <div class="hero-frame">
              <div class="hero-window">
                <div class="hero-art"></div>
              </div>
            </div>
            <div class="hero-card">
              <p>Индивидуальный подбор под интерьер</p>
              <span>Палитра из 9 теплых оттенков</span>
            </div>
          </div>
        </div>
      </section>

      <section class="catalog" id="catalog" v-if="section === 'home'">
        <div class="container">
          <div class="section-head">
            <div>
              <p class="eyebrow">Каталог</p>
              <h2>Деревянные рамки</h2>
            </div>
            <p class="section-note">Каждая рамка доступна в нескольких тонах дерева. Выберите модель и откройте полное описание.</p>
          </div>
          <div class="catalog-grid">
            <article
              v-for="(frame, index) in frames"
              :key="frame.id"
              class="frame-card"
              :style="{ '--tone': frame.tone, '--i': index }"
            >
              <div class="frame-image">
                <div class="frame-window">
                  <div class="frame-art"></div>
                </div>
              </div>
              <div class="frame-body">
                <div class="frame-title">
                  <h3>{{ frame.name }}</h3>
                  <span>{{ formatPrice(frame.price) }}</span>
                </div>
                <p>{{ frame.description }}</p>
                <div class="frame-tags">
                  <span>{{ frame.wood }}</span>
                  <span>{{ frame.size }}</span>
                  <span>{{ frame.finish }}</span>
                </div>
              </div>
              <a class="frame-link" :href="`#/frames/${frame.id}`">Открыть рамку</a>
            </article>
          </div>
        </div>
      </section>

      <section class="detail" v-else-if="section === 'details'">
        <div class="container">
          <a class="back-link" href="#/">← Назад к каталогу</a>
          <div v-if="currentFrame" class="detail-grid">
            <div class="detail-visual" :style="{ '--tone': currentFrame.tone }">
              <div class="detail-frame">
                <div class="detail-window">
                  <div class="detail-art"></div>
                </div>
              </div>
              <div class="detail-palette">
                <span v-for="(color, index) in currentFrame.palette" :key="`${currentFrame.id}-${index}`" :style="{ background: color }"></span>
              </div>
            </div>
            <div class="detail-info">
              <p class="eyebrow">Коллекция</p>
              <h2>{{ currentFrame.name }}</h2>
              <p class="lead">{{ currentFrame.description }}</p>
              <div class="price">{{ formatPrice(currentFrame.price) }}</div>
              <div class="specs">
                <div>
                  <span class="spec-title">Размер</span>
                  <span class="spec-value">{{ currentFrame.size }}</span>
                </div>
                <div>
                  <span class="spec-title">Дерево</span>
                  <span class="spec-value">{{ currentFrame.wood }}</span>
                </div>
                <div>
                  <span class="spec-title">Финиш</span>
                  <span class="spec-value">{{ currentFrame.finish }}</span>
                </div>
                <div>
                  <span class="spec-title">Стекло</span>
                  <span class="spec-value">{{ currentFrame.glass }}</span>
                </div>
                <div>
                  <span class="spec-title">Профиль</span>
                  <span class="spec-value">{{ currentFrame.profile }}</span>
                </div>
                <div>
                  <span class="spec-title">Глубина</span>
                  <span class="spec-value">{{ currentFrame.depth }}</span>
                </div>
                <div>
                  <span class="spec-title">Вес</span>
                  <span class="spec-value">{{ currentFrame.weight }}</span>
                </div>
                <div>
                  <span class="spec-title">Наличие</span>
                  <span class="spec-value">{{ currentFrame.availability }}</span>
                </div>
              </div>
              <div class="features">
                <h3>Особенности</h3>
                <ul>
                  <li v-for="feature in currentFrame.features" :key="feature">{{ feature }}</li>
                </ul>
              </div>
              <div class="care">
                <h3>Уход</h3>
                <p>{{ currentFrame.care }}</p>
              </div>
              <div class="detail-actions">
                <a class="button primary" href="#/contacts">Заказать эту рамку</a>
                <a class="button ghost" href="#/">Сравнить другие</a>
              </div>
            </div>
          </div>
          <div v-else class="not-found">
            <h2>Рамка не найдена</h2>
            <p>Проверьте ссылку или вернитесь к каталогу.</p>
          </div>
        </div>
      </section>

      <section class="contacts" v-else>
        <div class="container contacts-grid">
          <div class="contact-info">
            <p class="eyebrow">Контакты</p>
            <h2>Оформление заказа</h2>
            <p class="lead">
              Сообщите желаемую модель, размер и тон дерева — мы уточним сроки и подготовим подборку.
            </p>
            <div class="contact-card">
              <div>
                <span class="spec-title">Телефон</span>
                <span class="spec-value">+7 (495) 555-38-12</span>
              </div>
              <div>
                <span class="spec-title">Почта</span>
                <span class="spec-value">atelier@framewood.ru</span>
              </div>
              <div>
                <span class="spec-title">Адрес</span>
                <span class="spec-value">Москва, Большой Спасский пер., 9</span>
              </div>
              <div>
                <span class="spec-title">Время</span>
                <span class="spec-value">Пн–Сб · 10:00–20:00</span>
              </div>
            </div>
            <div class="delivery">
              <h3>Доставка и упаковка</h3>
              <p>
                Доставка по Москве в день готовности. В регионы — бережная упаковка и отправка транспортной компанией.
              </p>
            </div>
          </div>
          <form class="contact-form">
            <label>
              Имя
              <input type="text" name="name" placeholder="Как к вам обращаться" />
            </label>
            <label>
              Контакт
              <input type="text" name="contact" placeholder="Телефон или email" />
            </label>
            <label>
              Модель и размер
              <input type="text" name="model" placeholder="Например: Linea Nord, 30×40" />
            </label>
            <label>
              Комментарий
              <textarea name="comment" rows="4" placeholder="Дополнительные пожелания по тону и стеклу"></textarea>
            </label>
            <button class="button primary" type="submit">Отправить запрос</button>
            <p class="form-note">Мы отвечаем в течение рабочего дня.</p>
          </form>
        </div>
      </section>

      <section class="featured" v-if="section === 'home'">
        <div class="container">
          <div class="section-head">
            <div>
              <p class="eyebrow">Подборка</p>
              <h2>Тихая классика</h2>
            </div>
            <p class="section-note">Рамки с мягким профилем и глубокой посадкой.</p>
          </div>
          <div class="featured-grid">
            <article
              v-for="(frame, index) in featuredFrames"
              :key="`featured-${frame.id}`"
              class="featured-card"
              :style="{ '--tone': frame.tone, '--i': index }"
            >
              <div class="featured-frame">
                <div class="featured-window"></div>
              </div>
              <div>
                <h3>{{ frame.name }}</h3>
                <p>{{ frame.profile }} · {{ frame.size }}</p>
              </div>
              <span class="featured-price">{{ formatPrice(frame.price) }}</span>
            </article>
          </div>
        </div>
      </section>
    </main>

    <footer class="site-footer">
      <div class="container footer-inner">
        <div>
          <span class="logo-text">Framewood</span>
          <p>Минималистичные рамки из натурального дерева.</p>
        </div>
        <div class="footer-links">
          <a href="#/">Каталог</a>
          <a href="#/contacts">Контакты</a>
          <a href="#/contacts">Заказ</a>
        </div>
      </div>
    </footer>
  </div>
</template>
