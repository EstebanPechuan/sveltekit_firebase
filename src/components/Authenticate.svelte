<script>
    import { authHandlers } from "../store/store";

    let email = '';
    let password = '';
    let confirmPass = '';
    let error = false;
    let register = false;
    let authenticating = false;

    async function handleAuthenticate() {
        if (authenticating) {
            return
        }

        if (!email || !password || (register && !confirmPass)) {
            error = true
            return;
        }

        authenticating = true;
 
        try {
            if (!register) {
                await authHandlers.login(email, password)
            } else {
                await authHandlers.signup(email, password)
            }
        } catch(err) {
            console.log('There was an auth error', err);
            error = true
            authenticating = false
        }
    }

    function handleRegister() {
        register = !register
    }
</script>

<div class="authContainer">
    <form>
        <h1>{register ? 'Register' : 'Login'}</h1>
        {#if error}
            <p class="error">The information you have entered is not correct</p>
        {/if}

        <label for="email">
            <p class={email ? 'above': 'center'}>Email</p>

            <input bind:value={email} type="email" name="email" placeholder="Email">
        </label>
        
        <label for="password">
            <p class={password ? 'above': 'center'}>Password</p>

            <input bind:value={password} type="password" name="password" placeholder="Password">
        </label>
        
        {#if register}
            <label for="confirmPassword">
                <p class={confirmPass ? 'above': 'center'}>Confirm Password</p>

                <input bind:value={confirmPass} type="password" name="confirmPassword" placeholder="Confirm Password">
            </label>
        {/if}

        <button on:click={handleAuthenticate} type="button">
            {#if authenticating}
                Loading...
            {:else}
                Submit
            {/if}
        </button>
    </form>

    <div class="options">
        <p>Or</p>
        {#if register}
            <div>
                <p>Alredy have an account</p>
                <!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
                <p on:click={handleRegister} on:keydown={() => {}}>Login</p>
            </div>
        {:else}
            <div>
                <p>Don't have an account?</p>
                <!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
                <p on:click={handleRegister} on:keydown={() => {}}>Register</p>
            </div>
        {/if}
    </div>
</div>

<style>
    .authContainer {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        flex: 1;
        padding: 24px;
    }

    form {
        display: flex;
        flex-direction: column;
        gap: 14px;
    }

    form,
    .options {
        width: 400px;
        max-width: 100%;
        margin: 0 auto;
    }

    form label {
        position: relative;
        border: 1px solid navy;
        border-radius: 5px;
    }

    form input {
        width: 100%;
        border: none;
        background: transparent;
        color: white;
        outline: none;
        padding: 14px;
    }

    h1 {
        text-align: center;
        font-size: 3rem;
    }

    form button {
        background: navy;
        color: white;
        border: none;
        padding: 14px 0;
        border-radius: 5px;
        cursor: pointer;
        font-size: 1rem;
    }

    form button:hover {
        background: blue;
    }

    .above,
    .center {
        position: absolute;
        transform: translateY(-50%);
        pointer-events: none;
        color: white;
        border-radius: 4px;
        padding: 0 6px;
        font-size: 0.8rem;
    }

    .above {
        top: 0;
        left: 24px;
        background: navy;
        border: 1px solid blue;
        font-size: 0.7rem;
    }

    .center {
        top: 50%;
        left: 6px;
        border: 1px solid transparent;
        opacity: 0;
    }

    .error {
        color: coral;
        font-size: 0.9rem;
    }

    .options {
        padding: 14px 0;
        overflow: hidden;
        font-size: 0.9rem;
        display: flex;
        flex-direction: column;
        gap: 4px;
    }

    .options > p {
        position: relative;
        text-align: center;
        width: fit-content;
        margin: 0 auto;
        padding: 0 8px;
    }

    .options > p::after,
    .options > p::before {
        content: '';
        position: absolute;
        top: 50%;
        transform: translateY(-50%);
        width: 100vw;
        height: 1.5px;
        background-color: white;
    }

    .options > p::after {
        right: 100%;
    }

    .options > p::before {
        left: 100%;
    }

    .options div {
        display: flex;
        align-items: center;
        gap: 8px;
        justify-content: center;
    }

    .options div p:last-of-type {
        color: blue;
        cursor: pointer;
    }
</style>