<script>
    import Header from '../lib/header.svelte';
    import Menu from '../lib/menu.svelte';
</script>

<div>
    <div class="fixed">
        <Header></Header>
    </div>
    <div class="mt-s56-l64">
        <Menu></Menu>
    </div>
</div>

<style>
    .fixed {
        top: 0px;
        position: fixed;
    }

    @media screen and (max-width: 599px) {
        .mt-s56-l64 {
            margin-top: 56px;
        }
    }

    @media screen and (min-width: 600px) {
        .mt-s56-l64 {
            margin-top: 64px;
        }
    }
</style>