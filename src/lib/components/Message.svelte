<script lang="ts">
	import type { Message } from '$lib/models/message';

	export let message: Message;

	let loadInterval: NodeJS.Timeout;

	function loader() {
		message.text = '';

		loadInterval = setInterval(() => {
			// Update the text content of the loading indicator
			message.text += '.';

			// If the loading indicator has reached three dots, reset it
			if (message.text === '....') {
				message.text = '';
			}
		}, 300);
	}

	function typeText(text: string) {
		let index = 0;

		let interval = setInterval(() => {
			if (index < text.length) {
				message.text += text.charAt(index);
				index++;
			} else {
				clearInterval(interval);
			}
		}, 20);
	}

	async function fetchBotResponse() {
		if (message.input) {
			const response = await fetch('http://localhost:5173/', {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				},
				body: JSON.stringify({ input: message.input })
			});

			clearInterval(loadInterval);
			message.text = '';

			if (response.ok) {
				const data = await response.json();
				const parsedData = data.bot.trim(); // trims any trailing spaces/'\n'

				typeText(parsedData);
			} else {
				const err = await response.text();

				message.text = 'Something went wrong';
				alert(err);
			}
		}
	}

	if (message.isLoading) loader();
	if (message.isLoading) fetchBotResponse();
</script>

<div class={message.isBot ? 'bot-response' : ''}>
	<div class="container px-4 mx-auto flex gap-8 py-7">
		<div
			class={(message.isBot ? 'avatar-bot' : 'avatar-user') +
				' avatar h-12 rounded flex items-center justify-center'}
		>
			{#if message.isBot}
				<svg
					xmlns="http://www.w3.org/2000/svg"
					fill="none"
					viewBox="0 0 24 24"
					stroke-width="1.5"
					stroke="currentColor"
					class="w-7 h-7"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M8.25 3v1.5M4.5 8.25H3m18 0h-1.5M4.5 12H3m18 0h-1.5m-15 3.75H3m18 0h-1.5M8.25 19.5V21M12 3v1.5m0 15V21m3.75-18v1.5m0 15V21m-9-1.5h10.5a2.25 2.25 0 002.25-2.25V6.75a2.25 2.25 0 00-2.25-2.25H6.75A2.25 2.25 0 004.5 6.75v10.5a2.25 2.25 0 002.25 2.25zm.75-12h9v9h-9v-9z"
					/>
				</svg>
			{:else}
				<svg
					xmlns="http://www.w3.org/2000/svg"
					fill="none"
					viewBox="0 0 24 24"
					stroke-width="1.2"
					stroke="white"
					class="w-7 h-7"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z"
					/>
				</svg>
			{/if}
		</div>
		<p>{message.text}</p>
	</div>
</div>

<style>
	.avatar {
		flex: 0 0 3rem;
	}

	.avatar-user {
		background-color: #6c1f99;
	}

	.avatar-bot {
		background-color: #1f8999;
	}

	.bot-response {
		background-color: #122640;
	}
</style>
