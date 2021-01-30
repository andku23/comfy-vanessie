<template>
    <div class = "clue-container">
        <div>{{title}}</div>
    </div>
</template>

<script>
	export default {
		name: "clue",
		props: {
            title: {type: String, default: ''}
		}
	}
</script>

<style scoped>
.clue-container{
    justify-content: center;
    align-items: center;
    border-radius: 8px;
    cursor: pointer;
    outline: none;
    font-family: 'East Sea Dokdo', cursive;
    background: none;
    border: none;
    margin: 1px;
    background: #ffd1dc;
    text-shadow: 0 0 24px white;
    padding: 3px;
    color: #776990;
}
</style>