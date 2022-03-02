<script>
	export let info;
    let classes = "w-14 h-14 border-solid border-2 flex items-center justify-center mx-0.5 text-4xl font-bold rounded dark:text-white"
    $: if(info.matched==0){
        classes += ' absent shadowed bg-slate-400 dark:bg-slate-700 text-white border-slate-400 dark:border-slate-700'
    }
    $: if(info.matched==1){
        classes += ' correct shadowed bg-green-500 text-white border-green-500';
    }
    $: if(info.matched==2){
        classes += ' present shadowed bg-yellow-500 text-white border-yellow-500';
    }
</script>
<div class={classes}>
    {info.letter}
</div>
