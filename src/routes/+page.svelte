<script lang="ts">
	import { generateMnemonic } from '@scure/bip39';
	import { hexToBytes } from '@noble/hashes/utils';
	import { getPublicKey } from 'nostr-tools/pure';
	import { privateKeyFromSeedWords } from 'nostr-tools/nip06';
	import * as nip19 from 'nostr-tools/nip19';

	import { wordlist as chinese_simplified } from '@scure/bip39/wordlists/simplified-chinese';
	import { wordlist as chinese_traditional } from '@scure/bip39/wordlists/traditional-chinese';
	import { wordlist as czech } from '@scure/bip39/wordlists/czech';
	import { wordlist as english } from '@scure/bip39/wordlists/english';
	import { wordlist as french } from '@scure/bip39/wordlists/french';
	import { wordlist as italian } from '@scure/bip39/wordlists/italian';
	import { wordlist as japanese } from '@scure/bip39/wordlists/japanese';
	import { wordlist as korean } from '@scure/bip39/wordlists/korean';
	import { wordlist as spanish } from '@scure/bip39/wordlists/spanish';

	const languages = [
		{ name: 'English', list: english },
		{ name: '日本語', list: japanese },
		{ name: 'Español', list: spanish },
		{ name: '中文(简体)', list: chinese_simplified },
		{ name: '中文(繁體)', list: chinese_traditional },
		{ name: 'Français', list: french },
		{ name: 'Italiano', list: italian },
		{ name: '한국어', list: korean },
		{ name: 'Čeština', list: czech }
	];

	let language: typeof languages;
	let mnemonic: string = '';
	let passphrase: string = '';
	let accountIndex: number = 0;
	let privateKey: string = '';

	const generateSeedWord = (): void => {
		mnemonic = generateMnemonic(language.list);
	};

	const generateKey = (): void => {
		privateKey = privateKeyFromSeedWords(mnemonic, passphrase, accountIndex);
	};
</script>

<h1>NIP-06 tester</h1>
<p>
	🗨️: <select bind:value={language}>
		{#each languages as lang}
			<option value={lang}>{lang.name}</option>
		{/each}
	</select><button on:click={generateSeedWord}>🎲</button>
</p>
<p>🫘: <textarea bind:value={mnemonic} /></p>
<p>🔑: <input type="text" bind:value={passphrase} /></p>
<p>
	#️⃣: <select bind:value={accountIndex}>
		{#each Array(10) as _, i}
			<option value={i}>{i}</option>
		{/each}
	</select>
</p>
<p></p>
<p></p>
<button on:click={generateKey}>🤖</button>

{#if privateKey != ''}
	<div>
		<p>PRIVATE KEY(hex): {privateKey}</p>
		<p>PRIVATE KEY(nsec): {nip19.nsecEncode(hexToBytes(privateKey))}</p>
		<p>PUBLIC KEY(hex): {getPublicKey(hexToBytes(privateKey))}</p>
		<p>PUBLIC KEY(npub): {nip19.npubEncode(getPublicKey(hexToBytes(privateKey)))}</p>
	</div>
{/if}

<style>
	textarea {
		width: 300px;
		height: 50px;
		resize: none;
	}
</style>
