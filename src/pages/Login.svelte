<script>
import { Link } from 'svelte-routing'
import { signUp, signIn } from '../graphql/queries'
import AuthFacebook from '../components/AuthFacebook.svelte'
import AuthVk from '../components/AuthVk.svelte'
import { FACEBOOK_APP_ID, VK_APP_ID } from '../stores/auth'

let create = false, useSocial = false
let loginInput, passwordInput, rememberCheck

const login = () => {
  console.log('auth: signing in with discours.io account')
  const result = signIn(emailInput.value, passwordInput.value)
  console.debug(result)
}

const register = () => {
  console.log('auth: signing up with discours.io account')
  signUp(emailInput.value, passwordInput.value)
}

const providerSuccess = (e) => {
  console.dir(e.detail.user)
}

const providerFailure = (e) => {
  console.error(e)
}

</script>
<!-- svelte-ignore a11y-missing-attribute -->
<div class="view">
    <div class="auth">

      <div class="tabs" style="width: 100%; height: 54px; margin-top: 16px;">
        <div class="login-tab-title half" class:active={!create} on:click={() => create?(create=false):loginInput.focus()}>
          Вход
        </div>
        <div class="register-tab-title half" class:active={create} on:click={() => create?loginInput.focus():(create=true)}>
          Регистрация
        </div>
      </div>
      
      <div style="width: 100%; height: 33px; margin-top: 16px;">
        <div class="rowflex" style="justify-content: center;">
          <div style="width: 100%;">
            <input class="login-input" bind:this={loginInput} type="text" placeholder="Ваша почта"/>
          </div>
        </div>
      </div>
      
      <div style="width: 100%; height: 33px; margin-top: 16px;">
        <div class="rowflex" style="justify-content: center;">
          <div style="width: 100%;">
            <input class="password-input" bind:this={passwordInput} type="password" placeholder="Пароль"/>
          </div>
        </div>
      </div>
      
      <div style="width: 100%; margin-top: 16px;">
        <div class="half">
          <input type="checkbox" bind:this={rememberCheck} />
          <span style="line-height: 20px;">Запомнить</span>
        </div>
        <div class="half" style="color: rgba(158.93, 161.32, 167.47, 1);">
          <Link to="/resetpassword">Сброс пароля</Link>
        </div>
      </div>

      <div style="width: 100%; height: 33px; margin-top: 16px;">
        <div class="submitbtn" on:click={() => (create?register:login)()}>
          {create? 'Создать аккаунт' : 'Войти'}
        </div>
      </div>
      <Link to="/login" on:click={() => useSocial = !useSocial}>
        <div style="width: 100%; margin-top: 16px; color: black;" class:huge={useSocial} >
          {useSocial?'х':'Используя учётную запись в соцсети'}
        </div>
      </Link>
      {#if useSocial}
      <div style="width: 100%; height: 330px; margin-top: 16px;" class="social-provider">
        <div class="social">
          <AuthVk
            apiId={VK_APP_ID}
            on:auth-success={providerSuccess}
            on:auth-failure={providerFailure} />
          <AuthFacebook
            appId={FACEBOOK_APP_ID}
            on:auth-success={providerSuccess}
            on:auth-failure={providerFailure} />
        </div>
      </div>
      {/if}
    </div>
</div>

<style>  

  div {
    display: flex; 
    flex-direction: row; 
    align-items: center; 
    justify-content: center;
    min-width: 10%;
  }
  .huge { font-size: xx-large; }
  .view {
    width: 100%;
    height: 100%;
    display: inline-flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
  }
  .tabs {
    font-size: xx-large;
  }
  .rowflex {
    flex: 1 1 0%;
    height: 100%;
  }
  .password-input,
  .login-input {
    flex: 1 1 0%;
    font-size: 17px;
    line-height: 24px;
    border: none; outline: none;
  }
  .register-tab-title,
  .login-tab-title {
    height: 40px;
    flex-direction: column; 
    align-items: center;
    cursor: pointer;
    pointer-events: all;
    color: gray;
  }

  .half { 
    width: 50%;
  }
  
  .active { color: black; }

  .auth {
    display: inline-flex; 
    flex-direction: column;
    min-width: 45%;
  }
  .submitbtn {
    cursor: pointer;
    pointer-events: all;
    padding: 16px;
    display: flex;
    user-select: none;
    font-size: 25px;
    text-align: center;
    width: 100%;
    font-weight: 700;
    color: rgba(158.93, 161.32, 167.47, 1);
  }
  input[type="text"], input[type="password"] {
    border-bottom: 1px solid rgb(162, 162, 162);
  }
  .social {
    background-color: white !important;
    padding: 1em;
  }
</style>
