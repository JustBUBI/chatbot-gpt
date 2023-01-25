<script lang="ts">
	import '../app.css';
	import Message from '$lib/components/Message.svelte';
	import { writable } from 'svelte/store';

	export let messages = writable([
		{
			text: 'Hi there!',
			isBot: true,
			isLoading: false
		}
	]);

	let input: string = '';

	function submitText() {
		if (input.trim() != '') {
			const userMessage = { text: input, isBot: false, isLoading: false };
			const botMessage = { text: '', isBot: true, isLoading: true, input };
			messages.update((arr) => [...arr, userMessage, botMessage]);
			input = '';
		}
	}

	function handleKeyPress(event: KeyboardEvent) {
		if (event.key === 'Enter') {
			submitText();
		}
	}
</script>

<div class="w-full h-screen text-white flex flex-col justify-between gap-4">
	<!-- Chat section -->
	<div class="chat flex-1" id="chat_container">
		{#each $messages as message}
			<Message {message} />
		{/each}
	</div>

	<!-- Input section -->
	<form
		class="w-full px-4 mx-auto my-2 flex gap-2"
		on:submit|preventDefault={submitText}
		on:keydown={handleKeyPress}
	>
		<div class="relative w-full">
			<textarea
				bind:value={input}
				rows="1"
				cols="1"
				placeholder="Enter message..."
				class="w-full pl-4 pr-12 py-2 rounded"
			/>
			<button class="absolute right-4 top-2" type="submit">
				<svg
					xmlns="http://www.w3.org/2000/svg"
					fill="none"
					viewBox="0 0 24 24"
					stroke-width="1.5"
					stroke="currentColor"
					class="w-6 h-6"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M6 12L3.269 3.126A59.768 59.768 0 0121.485 12 59.77 59.77 0 013.27 20.876L5.999 12zm0 0h7.5"
					/>
				</svg>
			</button>
		</div>
	</form>
</div>

<style>
	form {
		max-width: 60rem;
	}

	textarea {
		background-color: #222d3b;
	}
</style>
