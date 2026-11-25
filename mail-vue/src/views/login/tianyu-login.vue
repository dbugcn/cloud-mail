<template>
  <div class="auth-page">
    <div class="topbar">
      <a class="logo" href="#" aria-label="天羽邮箱" @click.prevent>
        <span class="logo-mark" aria-hidden="true">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round">
            <rect x="3" y="5" width="18" height="14" rx="2"></rect>
            <path d="M3.5 7l8.5 6 8.5-6"></path>
          </svg>
        </span>
        <span>天羽<span class="logo-sub">邮箱</span></span>
      </a>
      <div class="topbar-actions">
        <button class="theme-toggle" type="button" :aria-label="isDark ? '切换到日间模式' : '切换到夜间模式'" :aria-pressed="isDark ? 'true' : 'false'" title="切换日间 / 夜间模式" @click="toggleTheme">
          <svg v-show="!isDark" class="icon-moon" viewBox="0 0 24 24" aria-hidden="true">
            <path d="M20 14.5A8 8 0 0 1 9.5 4a8 8 0 1 0 10.5 10.5z"></path>
          </svg>
          <svg v-show="isDark" class="icon-sun" viewBox="0 0 24 24" aria-hidden="true">
            <circle cx="12" cy="12" r="4"></circle>
            <path d="M12 2v2M12 20v2M4.9 4.9l1.4 1.4M17.7 17.7l1.4 1.4M2 12h2M20 12h2M4.9 19.1l1.4-1.4M17.7 6.3l1.4-1.4"></path>
          </svg>
        </button>
      </div>
    </div>

    <main class="main">
      <div class="layout">
        <section class="pitch" aria-labelledby="pitch-title">
          <h1 class="pitch-title" id="pitch-title">你的邮件<br>常联系</h1>
          <p class="pitch-text">属于你自己的私人邮箱。收信、写信，都安安静静地留在你的空间里，不被打扰。</p>
          <div class="pitch-art" aria-hidden="true">
            <svg class="envelope-art" viewBox="0 0 252 176" xmlns="http://www.w3.org/2000/svg">
              <defs>
                <linearGradient id="paperGradient" x1="0" y1="0" x2="0" y2="1">
                  <stop offset="0%" class="paper-stop-a"></stop>
                  <stop offset="100%" class="paper-stop-b"></stop>
                </linearGradient>
                <linearGradient id="flapGradient" x1="0" y1="0" x2="1" y2="1">
                  <stop offset="0%" class="flap-stop-a"></stop>
                  <stop offset="100%" class="flap-stop-b"></stop>
                </linearGradient>
                <clipPath id="envelopeClip">
                  <rect x="12" y="26" width="228" height="140" rx="14"></rect>
                </clipPath>
              </defs>
              <g transform="rotate(-5 126 96)">
                <rect class="env-paper" x="30" y="6" width="192" height="124" rx="10" stroke-width="1"></rect>
                <path class="env-lines" d="M50 32h66M50 50h120M50 68h94M50 86h108" fill="none" stroke-width="4" stroke-linecap="round"></path>
                <rect class="env-body" x="12" y="26" width="228" height="140" rx="14" stroke-width="1"></rect>
                <g clip-path="url(#envelopeClip)">
                  <path class="env-flap" d="M12 26 L126 110 L240 26 Z"></path>
                  <path class="env-crease" d="M12 26 L126 110 L240 26" fill="none" stroke-width="1.2" stroke-linejoin="round"></path>
                </g>
                <path class="env-seam" d="M12 160 L126 88 M240 160 L126 88" fill="none" stroke-width="1.2"></path>
                <circle class="env-dot-ring" cx="212" cy="46" r="11" fill="none" stroke-width="1.5"></circle>
                <circle class="env-dot-core" cx="212" cy="46" r="5"></circle>
              </g>
            </svg>
          </div>
        </section>

        <section class="login-card" aria-labelledby="login-title">
          <div class="card-head">
            <h2 class="card-title" id="login-title">{{ show === 'login' ? '登录邮箱' : (show === 'register' ? '注册邮箱' : '绑定邮箱') }}</h2>
            <p class="card-domain">{{ siteTitle }}</p>
          </div>

          <div v-if="oauthLoading" class="oauth-loading" aria-live="polite">正在完成第三方登录…</div>

          <form v-if="show === 'bind'" class="login-form" @submit.prevent="bind">
            <p class="bind-tip">请用一个已有邮箱完成绑定，之后即可用第三方账号登录。</p>
            <div class="field">
              <label for="bind-address">邮箱地址</label>
              <div class="input-wrap">
                <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <circle cx="12" cy="8" r="4"></circle>
                  <path d="M4 21c0-4 3.6-6.5 8-6.5s8 2.5 8 6.5"></path>
                </svg>
                <input id="bind-address" v-model="bindForm.email" type="text" autocomplete="username" :placeholder="$t('emailAccount')" @keyup.enter="bind">
                <span v-if="!hideLoginDomain" class="domain-suffix">{{ suffix }}</span>
              </div>
            </div>
            <div v-if="regKeyMode === 0" class="field">
              <label for="bind-code">{{ $t('regKey') }}</label>
              <div class="input-wrap">
                <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <rect x="4" y="10" width="16" height="10" rx="2"></rect>
                  <path d="M8 10V7a4 4 0 0 1 8 0v3"></path>
                </svg>
                <input id="bind-code" v-model="bindForm.code" type="text" :placeholder="$t('regKey')" @keyup.enter="bind">
              </div>
            </div>
            <div v-if="verifyShow" class="turnstile-wrap">
              <div :key="`bind-${turnstileContainerKey}`" ref="bindTurnstileContainer"></div>
              <span v-if="botJsError" class="turnstile-error">{{ $t('verifyModuleFailed', {action: '绑定'}) }}</span>
            </div>

            <button class="login-btn" type="submit" :disabled="bindLoading">{{ bindLoading ? '绑定中…' : '绑定并登录' }}</button>
          </form>

          <form v-if="show === 'login'" class="login-form" @submit.prevent="submit">
            <div class="field">
              <label for="mail-address">邮箱地址</label>
              <div class="input-wrap">
                <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <circle cx="12" cy="8" r="4"></circle>
                  <path d="M4 21c0-4 3.6-6.5 8-6.5s8 2.5 8 6.5"></path>
                </svg>
                <input id="mail-address" v-model="form.email" type="text" autocomplete="username" :placeholder="$t('emailAccount')" @keyup.enter="submit">
                <span v-if="!hideLoginDomain" class="domain-suffix">{{ suffix }}</span>
              </div>
            </div>
            <div class="field">
              <label for="mail-password">{{ $t('password') }}</label>
              <div class="input-wrap">
                <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <rect x="5" y="10" width="14" height="11" rx="2"></rect>
                  <path d="M8 10V7a4 4 0 0 1 8 0v3"></path>
                </svg>
                <input id="mail-password" v-model="form.password" type="password" autocomplete="current-password" :placeholder="$t('password')" @keyup.enter="submit">
              </div>
            </div>

            <div v-if="verifyShow" class="turnstile-wrap">
              <div :key="turnstileContainerKey" ref="loginTurnstileContainer"></div>
              <span v-if="botJsError" class="turnstile-error">{{ $t('verifyModuleFailed', {action: $t('loginBtn')}) }}</span>
            </div>

            <div class="form-row">
              <span class="form-hint">{{ $t('loginTitle') }}</span>
            </div>
            <button class="login-btn" type="submit" :disabled="loginLoading">{{ loginLoading ? '登录中…' : $t('loginBtn') }}</button>
          </form>

          <form v-else class="login-form" @submit.prevent="submitRegister">
            <div class="field">
              <label for="reg-address">邮箱地址</label>
              <div class="input-wrap">
                <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <circle cx="12" cy="8" r="4"></circle>
                  <path d="M4 21c0-4 3.6-6.5 8-6.5s8 2.5 8 6.5"></path>
                </svg>
                <input id="reg-address" v-model="registerForm.email" type="text" autocomplete="username" :placeholder="$t('emailAccount')" @keyup.enter="submitRegister">
                <span v-if="!hideLoginDomain" class="domain-suffix">{{ suffix }}</span>
              </div>
            </div>
            <div class="field">
              <label for="reg-password">{{ $t('password') }}</label>
              <div class="input-wrap">
                <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <rect x="5" y="10" width="14" height="11" rx="2"></rect>
                  <path d="M8 10V7a4 4 0 0 1 8 0v3"></path>
                </svg>
                <input id="reg-password" v-model="registerForm.password" type="password" autocomplete="new-password" :placeholder="$t('password')" @keyup.enter="submitRegister">
              </div>
            </div>
            <div class="field">
              <label for="reg-confirm">{{ $t('confirmPwd') }}</label>
              <div class="input-wrap">
                <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <rect x="5" y="10" width="14" height="11" rx="2"></rect>
                  <path d="M8 10V7a4 4 0 0 1 8 0v3"></path>
                </svg>
                <input id="reg-confirm" v-model="registerForm.confirmPassword" type="password" autocomplete="new-password" :placeholder="$t('confirmPwd')" @keyup.enter="submitRegister">
              </div>
            </div>
            <div v-if="regKeyMode !== 1" class="field">
              <label for="reg-code">{{ regKeyMode === 0 ? $t('regKey') : $t('regKeyOptional') }}</label>
              <div class="input-wrap">
                <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                  <rect x="4" y="10" width="16" height="10" rx="2"></rect>
                  <path d="M8 10V7a4 4 0 0 1 8 0v3"></path>
                </svg>
                <input id="reg-code" v-model="registerForm.code" type="text" :placeholder="regKeyMode === 0 ? $t('regKey') : $t('regKeyOptional')" @keyup.enter="submitRegister">
              </div>
            </div>

            <div v-if="verifyShow" class="turnstile-wrap">
              <div :key="turnstileContainerKey" ref="registerTurnstileContainer"></div>
              <span v-if="botJsError" class="turnstile-error">{{ $t('verifyModuleFailed', {action: $t('regBtn')}) }}</span>
            </div>

            <button class="login-btn" type="submit" :disabled="registerLoading">{{ registerLoading ? '注册中…' : $t('regBtn') }}</button>
          </form>

          <div v-if="oauthProviders.length" class="oauth-list">
            <button v-for="p in oauthProviders" :key="p.key" class="oauth-btn" type="button" @click="oauthLogin(p.key)">
              {{ p.label }}
            </button>
          </div>

          <div class="card-foot">
            <template v-if="show === 'login'">
              <a v-if="registerEnabled" href="#" @click.prevent="show = 'register'">{{ $t('noAccount') }} {{ $t('regSwitch') }}</a>
              <span v-else class="muted">账号由管理员统一开通</span>
            </template>
            <a v-else href="#" @click.prevent="show = 'login'">已有账号，返回登录</a>
          </div>
        </section>
      </div>
    </main>

    <footer class="footer">
      <p>
        <span>© {{ currentYear }} 天羽邮箱</span>
        <span class="sep" aria-hidden="true"></span>
        <span>由 cloudmail 提供支持的私人邮箱</span>
      </p>
    </footer>
  </div>
</template>

<script setup>
import router from "@/router";
import {useRoute} from "vue-router";
import {computed, nextTick, onBeforeUnmount, onMounted, reactive, ref, watch} from "vue";
import {login, register} from "@/request/login.js";
import {websiteConfig} from "@/request/setting.js";
import {isEmail} from "@/utils/verify-utils.js";
import {useSettingStore} from "@/store/setting.js";
import {useAccountStore} from "@/store/account.js";
import {useUserStore} from "@/store/user.js";
import {useUiStore} from "@/store/ui.js";
import {cvtR2Url} from "@/utils/convert.js";
import {loginUserInfo} from "@/request/my.js";
import {permsToRouter} from "@/perm/perm.js";
import {useI18n} from "vue-i18n";
import {oauthBindUser, oauthLinuxDoLogin, oauthGithubLogin, oauthGoogleLogin} from "@/request/ouath.js";
import {reloadTurnstileScript} from "@/utils/turnstile-script.js";

const {t} = useI18n();
const accountStore = useAccountStore();
const userStore = useUserStore();
const uiStore = useUiStore();
const settingStore = useSettingStore();
const route = useRoute();

const show = ref('login')
const loginLoading = ref(false)
const registerLoading = ref(false)
const bindLoading = ref(false)
const oauthLoading = ref(false)
const form = reactive({ email: '', password: '' })
const registerForm = reactive({ email: '', password: '', confirmPassword: '', code: null })
const bindForm = reactive({ email: '', oauthUserId: '', code: '' })

const domainList = settingStore.domainList;
const suffix = ref('')
if (domainList.length > 0) suffix.value = domainList[0]

const currentYear = new Date().getFullYear()
const isDark = ref(uiStore.dark === true)
const siteTitle = computed(() => settingStore.settings.title || 'mail.syszhiy.com')
const hideLoginDomain = computed(() => settingStore.settings.loginDomain === 1)
const registerEnabled = computed(() => settingStore.settings.register === 0)
const regKeyMode = computed(() => settingStore.settings.regKey)
const oauthKeys = ['linuxdo', 'github', 'google']
const oauthProvider = computed(() => {
  const fromState = route.query.state
  if (oauthKeys.includes(fromState)) return fromState
  const fromStore = sessionStorage.getItem('oauthProvider')
  return oauthKeys.includes(fromStore) ? fromStore : null
})
const oauthProviders = computed(() => {
  const all = [
    {key: 'google', label: 'Google'},
    {key: 'github', label: 'GitHub'},
    {key: 'linuxdo', label: 'LinuxDo'},
  ]
  return all.filter(p => settingStore.settings[p.key + 'Switch'] === 0)
})

function toggleTheme() {
  isDark.value = !isDark.value
  uiStore.dark = isDark.value
  document.documentElement.classList.toggle('dark', isDark.value)
}

const backgroundStyle = computed(() => {
  return settingStore.settings.background ? {
    'background-image': `url(${cvtR2Url(settingStore.settings.background)})`,
    'background-repeat': 'no-repeat',
    'background-size': 'cover',
    'background-position': 'center'
  } : {}
})

const verifyShow = ref(false)
const botJsError = ref(false)
const turnstileContainerKey = ref(0)
const loginTurnstileContainer = ref(null)
const registerTurnstileContainer = ref(null)
const bindTurnstileContainer = ref(null)
let verifyToken = ''
let turnstileId = null
let turnstileLoading = false
const turnstileContainer = computed(() => {
  if (show.value === 'login') return loginTurnstileContainer.value
  if (show.value === 'register') return registerTurnstileContainer.value
  return bindTurnstileContainer.value
})

function clearTurnstile() {
  if (turnstileId !== null) window.turnstile?.remove(turnstileId)
  turnstileId = null
  verifyShow.value = false
  botJsError.value = false
  verifyToken = ''
}

function verifyConfigForCurrentTab() {
  if (show.value === 'register') {
    return {mode: settingStore.settings.registerVerify, open: settingStore.settings.regVerifyOpen}
  }
  return {mode: settingStore.settings.loginVerify, open: settingStore.settings.loginVerifyOpen}
}

function shouldVerify(mode, open) {
  return mode === 0 || (mode === 2 && open === true)
}

function autoShowTurnstile() {
  if (!settingStore.settings.siteKey) return
  const {mode, open} = verifyConfigForCurrentTab()
  if (shouldVerify(mode, open)) showTurnstile()
}

watch(show, () => {
  clearTurnstile()
  nextTick(autoShowTurnstile)
})
onBeforeUnmount(clearTurnstile)

onMounted(autoShowTurnstile)

function renderTurnstile(api, container) {
  turnstileId = api.render(container, {
    sitekey: settingStore.settings.siteKey,
    size: 'flexible',
    callback: (token) => {
      if (turnstileContainer.value !== container) return
      verifyToken = token
      botJsError.value = false
    },
    'expired-callback': () => {
      if (turnstileContainer.value === container) verifyToken = ''
    },
    'error-callback': () => {
      if (turnstileContainer.value !== container) return true
      verifyToken = ''
      botJsError.value = true
      return true
    }
  })
}

async function showTurnstile() {
  if (turnstileLoading) return
  verifyToken = ''
  botJsError.value = false
  verifyShow.value = true
  turnstileLoading = true
  await nextTick()
  let container = turnstileContainer.value
  try {
    if (!container) return
    if (turnstileId !== null) {
      window.turnstile.reset(turnstileId)
    } else {
      renderTurnstile(window.turnstile, container)
    }
  } catch (error) {
    if (turnstileContainer.value !== container) return
    turnstileId = null
    turnstileContainerKey.value++
    await nextTick()
    container = turnstileContainer.value
    try {
      if (!container) return
      const api = await reloadTurnstileScript()
      if (turnstileContainer.value === container) renderTurnstile(api, container)
    } catch (error) {
      if (turnstileContainer.value === container) botJsError.value = true
      console.warn('Turnstile recovery failed', error)
    }
  } finally {
    turnstileLoading = false
  }
}

function needsTurnstile(mode, open) {
  if (verifyToken || (mode !== 0 && !(mode === 2 && open))) return false
  if (!verifyShow.value || botJsError.value) showTurnstile()
  else if (!turnstileLoading) ElMessage({message: t('botVerifyMsg'), type: 'error', plain: true})
  return true
}

const getFullEmail = (email) => hideLoginDomain.value ? email : email + suffix.value
const getEmailName = (email) => email.split('@')[0]

function oauthLogin(provider) {
  const clientId = settingStore.settings[provider + 'ClientId']
  const redirectUri = encodeURIComponent(window.location.origin + '/login')
  sessionStorage.setItem('oauthProvider', provider)
  const authorizeUrls = {
    linuxdo: `https://connect.linux.do/oauth2/authorize?client_id=${clientId}&redirect_uri=${redirectUri}&response_type=code&scope=openid+profile+email&state=${provider}`,
    github: `https://github.com/login/oauth/authorize?client_id=${clientId}&redirect_uri=${redirectUri}&scope=user:email&state=${provider}`,
    google: `https://accounts.google.com/o/oauth2/v2/auth?client_id=${clientId}&redirect_uri=${redirectUri}&response_type=code&scope=openid+profile+email&state=${provider}`,
  }
  window.location.href = authorizeUrls[provider]
}

const loginFns = {
  linuxdo: oauthLinuxDoLogin,
  github: oauthGithubLogin,
  google: oauthGoogleLogin,
}

oauthGetUser();

async function oauthGetUser() {
  const params = new URLSearchParams(window.location.search)
  const code = params.get('code')
  if (!code || !oauthProvider.value) return
  const provider = oauthProvider.value
  oauthLoading.value = true
  sessionStorage.removeItem('oauthProvider')
  window.history.replaceState({}, '', window.location.origin + window.location.pathname)
  loginFns[provider](code, window.location.origin + '/login').then(data => {
    bindForm.oauthUserId = data.userInfo.oauthUserId;
    if (!data.token) {
      oauthLoading.value = false
      show.value = 'bind'
      ElMessage({message: '请用一个已有邮箱完成绑定', type: 'warning', duration: 4000, plain: true})
      return
    }
    saveToken(data.token)
  }).catch(() => {
    oauthLoading.value = false
  })
}

const bind = () => {
  if (bindLoading.value) return
  if (!bindForm.email) {
    ElMessage({message: t('emptyEmailMsg'), type: 'error', plain: true})
    return
  }
  if (getEmailName(bindForm.email).length < settingStore.settings.minEmailPrefix) {
    ElMessage({message: t('minEmailPrefix', {msg: settingStore.settings.minEmailPrefix}), type: 'error', plain: true})
    return
  }
  const email = getFullEmail(bindForm.email)
  if (!isEmail(email)) {
    ElMessage({message: t('notEmailMsg'), type: 'error', plain: true})
    return
  }
  if (regKeyMode.value === 0 && !bindForm.code) {
    ElMessage({message: t('emptyRegKeyMsg'), type: 'error', plain: true})
    return
  }
  bindLoading.value = true
  oauthBindUser({email, oauthUserId: bindForm.oauthUserId, code: bindForm.code}).then(data => {
    saveToken(data.token)
  }).catch(() => {
    bindLoading.value = false
  })
}

const submit = () => {
  if (loginLoading.value) return
  if (!form.email) {
    ElMessage({message: t('emptyEmailMsg'), type: 'error', plain: true})
    return
  }
  const email = getFullEmail(form.email)
  if (!isEmail(email)) {
    ElMessage({message: t('notEmailMsg'), type: 'error', plain: true})
    return
  }
  if (!form.password) {
    ElMessage({message: t('emptyPwdMsg'), type: 'error', plain: true})
    return
  }
  const {loginVerify, loginVerifyOpen} = settingStore.settings
  if (needsTurnstile(loginVerify, loginVerifyOpen)) return
  const token = verifyToken
  loginLoading.value = true
  login(email, form.password, token).then(data => saveToken(data.token)).catch(res => {
    if (res.code === 400) settingStore.settings.loginVerifyOpen = true
    if (show.value === 'login' && (token || res.code === 400)) showTurnstile()
  }).finally(() => {
    loginLoading.value = false
  })
}

async function saveToken(token) {
  localStorage.setItem('token', token)
  refreshWebsiteConfig()
  const user = await loginUserInfo();
  accountStore.currentAccountId = user.account.accountId;
  accountStore.currentAccount = user.account;
  userStore.user = user;
  const routers = permsToRouter(user.permKeys);
  routers.forEach(routerData => router.addRoute('layout', routerData));
  await router.replace({name: 'layout'})
  uiStore.showNotice()
  oauthLoading.value = false
  bindLoading.value = false
}

function refreshWebsiteConfig() {
  websiteConfig().then(setting => {
    settingStore.settings = setting
    settingStore.domainList = setting.domainList
    if (!suffix.value && setting.domainList.length > 0) suffix.value = setting.domainList[0]
    document.title = setting.title
  }).catch(e => console.error(e))
}

const submitRegister = () => {
  if (registerLoading.value) return
  if (!registerForm.email) {
    ElMessage({message: t('emptyEmailMsg'), type: 'error', plain: true})
    return
  }
  if (getEmailName(registerForm.email).length < settingStore.settings.minEmailPrefix) {
    ElMessage({message: t('minEmailPrefix', {msg: settingStore.settings.minEmailPrefix}), type: 'error', plain: true})
    return
  }
  const email = getFullEmail(registerForm.email)
  if (!isEmail(email)) {
    ElMessage({message: t('notEmailMsg'), type: 'error', plain: true})
    return
  }
  if (!registerForm.password) {
    ElMessage({message: t('emptyPwdMsg'), type: 'error', plain: true})
    return
  }
  if (registerForm.password.length < 6) {
    ElMessage({message: t('pwdLengthMsg'), type: 'error', plain: true})
    return
  }
  if (registerForm.password !== registerForm.confirmPassword) {
    ElMessage({message: t('confirmPwdFailMsg'), type: 'error', plain: true})
    return
  }
  if (regKeyMode.value === 0 && !registerForm.code) {
    ElMessage({message: t('emptyRegKeyMsg'), type: 'error', plain: true})
    return
  }
  const {registerVerify, regVerifyOpen} = settingStore.settings
  if (needsTurnstile(registerVerify, regVerifyOpen)) return
  registerLoading.value = true
  const payload = {
    email,
    password: registerForm.password,
    token: verifyToken,
    code: registerForm.code
  }
  register(payload).then(({regVerifyOpen}) => {
    show.value = 'login'
    registerForm.email = ''
    registerForm.password = ''
    registerForm.confirmPassword = ''
    registerForm.code = null
    registerLoading.value = false
    settingStore.settings.regVerifyOpen = regVerifyOpen
    ElMessage({message: t('regSuccessMsg'), type: 'success', plain: true})
  }).catch(res => {
    registerLoading.value = false
    if (res.code === 400) settingStore.settings.regVerifyOpen = true
    if (show.value === 'register' && (payload.token || res.code === 400)) showTurnstile()
  })
}
</script>

<style>
:root {
  --brand: #0052D9;
  --brand-deep: #0043B5;
  --brand-ink: #123C8C;
  --sky: #EAF3FF;
  --sky-mid: #F6FAFF;
  --sky-deep: #DCEBFF;
  --card: #FFFFFF;
  --field: #F7FAFF;
  --ink: #1F2E42;
  --slate: #6E7F95;
  --line: #DCE5F2;
  --line-hover: #B9CBE8;
  --art-halo: rgba(0, 82, 217, 0.10);
  --art-shadow: rgba(18, 60, 140, 0.18);
  --env-face: #FFFFFF;
  --env-outline: rgba(0, 82, 217, 0.18);
  --env-crease: rgba(255, 255, 255, 0.60);
  --env-seam: rgba(0, 82, 217, 0.10);
  --paper-top: #FFFFFF;
  --paper-bottom: #F1F6FF;
  --paper-line-color: rgba(18, 60, 140, 0.20);
  --flap-start: #5B9BFB;
  --flap-end: #0052D9;
  --dot: #FFB400;
  --dot-halo: rgba(255, 180, 0, 0.30);
  --shadow: 0 20px 50px rgba(18, 60, 140, 0.12), 0 2px 8px rgba(18, 60, 140, 0.05);
  --page-glow: rgba(255, 255, 255, 0.9);
}
.dark {
  --brand: #4B8DFF;
  --brand-deep: #3679F0;
  --brand-ink: #B9C9E6;
  --sky: #0D1117;
  --sky-mid: #121722;
  --sky-deep: #0A0E14;
  --card: #161C26;
  --field: #1B222D;
  --ink: #E8ECF4;
  --slate: #A9B4C5;
  --line: #2B3444;
  --line-hover: #3D4A5F;
  --art-halo: rgba(43, 84, 168, 0.28);
  --art-shadow: rgba(0, 0, 0, 0.55);
  --env-face: #1E2734;
  --env-outline: rgba(75, 141, 255, 0.26);
  --env-crease: rgba(255, 255, 255, 0.28);
  --env-seam: rgba(75, 141, 255, 0.18);
  --paper-top: #202838;
  --paper-bottom: #161C26;
  --paper-line-color: rgba(169, 180, 197, 0.24);
  --flap-start: #3679F0;
  --flap-end: #1D3F7A;
  --dot: #FFC44D;
  --dot-halo: rgba(255, 196, 77, 0.28);
  --shadow: 0 18px 50px rgba(0, 0, 0, 0.55), 0 2px 8px rgba(0, 0, 0, 0.40);
  --page-glow: transparent;
}
html:has(.auth-page) body { background:transparent; }
</style>

<style scoped>
.auth-page { min-height:100vh; color:var(--ink); font-family:"Noto Sans SC","PingFang SC","Microsoft YaHei","Segoe UI",sans-serif; font-size:16px; line-height:1.7; -webkit-font-smoothing:antialiased;
  background:radial-gradient(900px 420px at 12% -10%, var(--page-glow) 0%, transparent 60%), linear-gradient(165deg,var(--sky) 0%,var(--sky-mid) 52%,var(--sky-deep) 100%); }
.topbar { max-width:1180px; margin:0 auto; padding:26px clamp(20px,4vw,48px) 0; display:flex; align-items:center; justify-content:space-between; gap:20px; }
.logo { display:inline-flex; align-items:center; gap:10px; text-decoration:none; color:var(--brand); font-size:1.0625rem; font-weight:700; }
.logo-mark { display:grid; place-items:center; width:34px; height:34px; border-radius:9px; background:var(--brand); color:#fff; }
.logo-mark svg { width:19px; height:19px; display:block; }
.logo-sub { font-weight:400; color:var(--slate); }
.topbar-actions { display:flex; align-items:center; gap:26px; }
.theme-toggle { display:grid; place-items:center; width:36px; height:36px; padding:0; border:1px solid var(--line); border-radius:50%; background:var(--card); color:var(--brand); cursor:pointer; transition:background .16s ease,border-color .16s ease; }
.theme-toggle:hover { background:var(--field); border-color:var(--line-hover); }
.theme-toggle svg { width:18px; height:18px; display:block; fill:none; stroke:currentColor; stroke-width:1.8; stroke-linecap:round; stroke-linejoin:round; }
.main { max-width:1180px; margin:0 auto; padding:clamp(36px,6vw,76px) clamp(20px,4vw,48px) clamp(48px,8vw,96px); }
.layout { display:grid; grid-template-columns:minmax(0,1fr) minmax(0,400px); align-items:center; gap:clamp(40px,7vw,110px); }
.pitch { min-width:0; }
.pitch-title { margin:0 0 22px; color:var(--brand-ink); font-size:clamp(2.4rem,4.6vw,3.5rem); font-weight:700; line-height:1.22; letter-spacing:-.01em; text-wrap:balance; }
.pitch-text { margin:0; max-width:30rem; font-size:1.0625rem; line-height:1.85; color:var(--slate); }
.pitch-art { position:relative; margin-top:48px; height:200px; }
.pitch-art::before { content:""; position:absolute; left:-24px; top:-18px; width:264px; height:264px; border-radius:50%; background:radial-gradient(circle,var(--art-halo) 0%,transparent 70%); }
.envelope-art { position:absolute; left:2px; top:8px; width:252px; height:176px; overflow:visible; filter:drop-shadow(0 12px 20px var(--art-shadow)); shape-rendering:geometricPrecision; }
:deep(.paper-stop-a) { stop-color:var(--paper-top); }
:deep(.paper-stop-b) { stop-color:var(--paper-bottom); }
:deep(.flap-stop-a) { stop-color:var(--flap-start); }
:deep(.flap-stop-b) { stop-color:var(--flap-end); }
:deep(.env-paper) { fill:url(#paperGradient); stroke:var(--env-outline); }
:deep(.env-lines) { stroke:var(--paper-line-color); }
:deep(.env-body) { fill:var(--env-face); stroke:var(--env-outline); }
:deep(.env-flap) { fill:url(#flapGradient); }
:deep(.env-crease) { stroke:var(--env-crease); }
:deep(.env-seam) { stroke:var(--env-seam); }
:deep(.env-dot-ring) { stroke:var(--dot-halo); }
:deep(.env-dot-core) { fill:var(--dot); }
.login-card { width:100%; background:var(--card); border-radius:12px; padding:38px 36px 30px; box-shadow:var(--shadow); }
.card-head { margin-bottom:28px; }
.card-head::before { content:""; display:block; width:32px; height:3px; margin-bottom:18px; border-radius:2px; background:var(--brand); }
.card-title { margin:0; font-size:1.5rem; font-weight:600; line-height:1.35; color:var(--ink); }
.card-domain { margin:4px 0 0; font-size:.8125rem; color:var(--slate); word-break:break-all; }
.oauth-loading { margin-bottom:18px; padding:10px 12px; border-radius:6px; background:var(--field); color:var(--slate); font-size:.875rem; }
.bind-tip { margin:0 0 4px; font-size:.8125rem; line-height:1.6; color:var(--slate); }
.login-form { display:grid; gap:18px; }
.field { display:grid; gap:7px; }
.field label { font-size:.875rem; font-weight:500; color:var(--ink); }
.input-wrap { position:relative; display:flex; align-items:center; }
.input-icon { position:absolute; left:13px; width:16px; height:16px; color:#98A8BE; pointer-events:none; }
.field input { width:100%; padding:11px 14px 11px 40px; background:var(--field); border:1px solid var(--line); border-radius:6px; font-family:inherit; font-size:.9375rem; line-height:1.6; color:var(--ink); transition:background .16s ease,border-color .16s ease,box-shadow .16s ease; }
.field input::placeholder { color:#9BA9BC; }
.field input:hover { border-color:var(--line-hover); }
.field input:focus { outline:none; background:var(--card); border-color:var(--brand); box-shadow:0 0 0 3px rgba(0,82,217,.12); }
.domain-suffix { position:absolute; right:12px; color:var(--slate); font-size:.8125rem; pointer-events:none; }
.input-wrap:has(.domain-suffix) input { padding-right:120px; }
.turnstile-wrap { min-height:64px; }
.turnstile-error { display:block; margin-top:6px; font-size:12px; color:#E24B4A; }
.form-row { display:flex; align-items:center; justify-content:space-between; gap:14px; font-size:.8125rem; color:var(--slate); }
.form-hint { line-height:1.5; }
.login-btn { margin-top:2px; padding:12px 18px; width:100%; background:var(--brand); color:#fff; border:0; border-radius:6px; font-family:inherit; font-size:1rem; font-weight:600; line-height:1.6; cursor:pointer; transition:background .16s ease,transform .16s ease; }
.login-btn:hover { background:var(--brand-deep); }
.login-btn:active { transform:translateY(1px); }
.login-btn:disabled { opacity:.6; cursor:default; }
.oauth-list { display:grid; gap:10px; margin-top:18px; }
.oauth-btn { padding:10px 14px; background:transparent; border:1px solid var(--line); border-radius:6px; color:var(--ink); font-family:inherit; font-size:.875rem; cursor:pointer; }
.oauth-btn:hover { border-color:var(--brand); color:var(--brand); }
.card-foot { display:flex; align-items:center; justify-content:center; gap:16px; margin-top:24px; padding-top:22px; border-top:1px solid var(--line); font-size:.875rem; }
.card-foot a { color:var(--brand); text-decoration:none; cursor:pointer; }
.card-foot a:hover { text-decoration:underline; }
.card-foot .muted { color:var(--slate); }
.footer { max-width:1180px; margin:0 auto; padding:0 clamp(20px,4vw,48px) 28px; text-align:center; font-size:.8125rem; color:var(--slate); }
.footer p { display:inline-flex; align-items:center; justify-content:center; flex-wrap:wrap; gap:14px; margin:0; }
.footer .sep { width:1px; height:12px; background:var(--line); }
@keyframes rise { from { opacity:0; transform:translateY(14px); } to { opacity:1; transform:none; } }
.login-card { animation:rise .6s cubic-bezier(.2,.7,.2,1) both; }
@media (prefers-reduced-motion: reduce) { .login-card { animation:none; } }
@media (max-width:880px) {
  .topbar { padding-top:22px; }
  .main { padding-top:34px; display:flex; justify-content:center; }
  .layout { display:block; width:100%; max-width:420px; }
  .pitch { display:none; }
}
@media (max-width:560px) {
  .topbar { padding:20px 20px 0; }
  .main { padding:30px 16px 40px; }
  .login-card { padding:32px 24px 26px; border-radius:12px; }
  .card-title { font-size:1.375rem; }
  .footer { padding-bottom:22px; }
}
</style>
