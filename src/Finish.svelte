
<script>
    export let status;
</script>
{#if status === 1 || status === 2}
    <div class="backdrop" class:promo={true}>
    <div class="modal">
    <p>{status===1 ? "success": "failure"}</p>
    </div>
    </div>
    {/if}
    <style>
    .backdrop {
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.8);
    }
    .modal {
    padding: 10px;
    border-radius: 10px;
    max-width: 400px;
    margin: 10% auto;
    text-align: center;
    background: white;
    }
    .promo .modal {
    background: crimson;
    color: white;
    }
</style>
    
    
