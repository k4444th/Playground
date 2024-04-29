<script>
    import Snake from './snake.svelte';
    
    import Tab, { Label } from '@smui/tab';
    import TabBar from '@smui/tab-bar';

    let tabs = [
            'Snake',
            'Tetris',
            'Minesweeper'
        ];
    let active = tabs[0];
</script>

<div>
    <TabBar {tabs} let:tab bind:active>
        <Tab {tab}>
          <Label>{tab}</Label>
        </Tab>
    </TabBar>
    <div class="content">
        {#if active === tabs[0]}
            <Snake></Snake>
        {:else if active === tabs[1]}
             Tetris
        {:else if active === tabs[2]}
             Minesweeper
        {/if}
    </div>
</div>

<style>
    @media screen and (max-width: 599px) {
        .content {
            margin: 1rem;
        }
    }

    @media screen and (min-width: 600px) {
        .content {
            margin: 2rem;
        }
    }
</style>