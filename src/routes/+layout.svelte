<script lang="ts">
    import "./../app.css";
    import { Header } from "$components";
    import {invalidate} from "$app/navigation";
    import {onMount} from "svelte";
    import {setUserState} from "$lib/state/user-state.svelte";

    let {children, data} = $props();       
    let {session, supabase, user} = $derived(data);

    let userState = setUserState({session: data.session, supabase: data.supabase, user: data.user});

    $effect(() => {
        userState.updateState({session, supabase, user});
    })


    $effect(() => {
        const {data} = supabase.auth.onAuthStateChange((_, newSession) => {
            if (newSession?.expires_at !== session?.expires_at) {
                invalidate("supabase:auth");
            }
        });
        return () => data.subscription.unsubscribe();
    })

    /*
    onMount(() => {
        const {data} = supabase.auth.onAuthStateChange((_, newSession) => {
            if (newSession?.expires_at !== session?.expires_at) {
                invalidate("supabase:auth");
            }
        })
    })
    */
</script>
<Header />
{@render children()}