<script setup lang='ts'>
	import { computed, onMounted, ref } from 'vue'
	import { NSpin } from 'naive-ui'
	import pkg from '../../../../package.json'
	import { fetchChatConfig } from '@/api'
	import { useAuthStore } from '@/store'

	interface ConfigState {
		timeoutMs?: number
		reverseProxy?: string
		apiModel?: string
		socksProxy?: string
		httpsProxy?: string
		usage?: string
	}

	const authStore = useAuthStore()

	const loading = ref(false)

	const config = ref<ConfigState>()

	const isChatGPTAPI = computed<boolean>(() => !!authStore.isChatGPTAPI)

	async function fetchConfig() {
		try {
			loading.value = true
			const { data } = await fetchChatConfig<ConfigState>()
			config.value = data
		}
		finally {
			loading.value = false
		}
	}

	onMounted(() => {
		fetchConfig()
	})
</script>

<template>
	<NSpin :show="loading">
		<div class="p-4 space-y-4">
			<h2 class="text-xl font-bold">
				Version - {{ pkg.version }}
			</h2>
			<div class="p-2 space-y-2 rounded-md bg-neutral-100 dark:bg-neutral-700">
				<p>
					免费用于学习和测试,禁止发布、传播任何违法、违规内容，使用本网站，视您接受并同意
					<a class="text-blue-600 dark:text-blue-500"
						 href="http://xiamu.3vkj.club/open_ai/WebsiteDisclaimer.html"
						 target="_blank">
						《免责声明》
					</a>
				</p>
				<!--<p>
					为了防止失联请收藏最新地址：
					<a class="text-blue-600 dark:text-blue-500"
						 href="https://ca6ssmq5g6hbkhm7c3qg.baseapi.memfiredb.com/storage/v1/object/public/xiamu/ChatGPT.html"
						 target="_blank">
						ChatGPT
					</a>
				</p>-->
			</div>
			<p>{{ $t("setting.api") }}：{{ config?.apiModel ?? '-' }}</p>
			<p v-if="isChatGPTAPI">
				{{ $t("setting.monthlyUsage") }}：{{ config?.usage ?? '-' }}
			</p>
			<p v-if="!isChatGPTAPI">
				{{ $t("setting.reverseProxy") }}：{{ config?.reverseProxy ?? '-' }}
			</p>
			<p>{{ $t("setting.timeout") }}：{{ config?.timeoutMs ?? '-' }}</p>
			<p>{{ $t("setting.socks") }}：{{ config?.socksProxy ?? '-' }}</p>
			<p>{{ $t("setting.httpsProxy") }}：{{ config?.httpsProxy ?? '-' }}</p>
		</div>
	</NSpin>
</template>
