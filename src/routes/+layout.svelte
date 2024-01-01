<script>
// @ts-nocheck

    import { onMount } from "svelte";
    import { auth, db } from "$lib/firebase/firebase";
    import { doc, getDoc, setDoc } from "firebase/firestore";
    import { authStore } from "../store/store";

    const nonAuthRoutes = ['/']

    onMount(() => {
        console.log('Mounting');
        const unsubscribe = auth.onAuthStateChanged(async user => {
            const currentPath = window.location.pathname

            if (!user && !nonAuthRoutes.includes(currentPath)) {
                window.location.href = "/"
                return
            }

            if (user && currentPath === '/') {
                window.location.href = '/dashboard'
                return
            }

            if (!user) {
                return
            }

            let dataToSetStore

            const docRef = doc(db, 'users', user.uid)
            const docSnap = await getDoc(docRef)
            if (!docSnap.exists()) {
                const userRef = doc(db, 'users', user.uid)

                dataToSetStore = {
                    email: user.email,
                    todos: []
                }
                
                await setDoc(
                    userRef,
                    dataToSetStore,
                    { merge: true }
                )
            } else {
                const userData = docSnap.data()
                dataToSetStore = userData
            }

            authStore.update(curr => {
                return {
                    ...curr,
                    user,
                    data: dataToSetStore,
                    loading: false
                }
            })
        })
    })
</script>

<svelte:head>
    <title>ToDo List</title>
</svelte:head>

<div class="mainContainer">
    <slot />
</div>

<style>
    .mainContainer {
        min-height: 100vh;
        background: linear-gradient(to right, #000428, #000046);
        color: white;
        position: relative;
        display: flex;
        flex-direction: column;
    }
</style>