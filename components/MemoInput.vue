<template>
  <div class="p-2 sm:p-4 pb-2 border-b dark:border-white">
    <div class="flex flex-row my-2 ">
      <div class="flex flex-1 gap-2 ">

        <Popover :open="linkOpen">
          <PopoverTrigger as="div">
            <TooltipProvider>
              <Tooltip>
                <TooltipTrigger as-child>
                  <Link :stroke-width="1.5" class="cursor-pointer w-[20px] h-[20px]" @click="linkOpen = true" />
                </TooltipTrigger>
                <TooltipContent>
                  <p>插入链接</p>
                </TooltipContent>
              </Tooltip>
            </TooltipProvider>

          </PopoverTrigger>
          <PopoverContent as-child @interact-outside="linkOpen = false">
            <div class="flex flex-col gap-2">
              <div class="text-xs my-2 flex justify-between"><span>插入链接</span>
              </div>
              <Input class="my-2" placeholder="请输入链接地址" v-model="externalUrl" />
              <template v-if="externalFetchError">
                <Input class="my-2" placeholder="请输入链接标题" v-model="externalTitle" />
                <Input class="my-2" placeholder="请输入链接图标,选填" v-model="externalFavicon" />
              </template>
              <div class="text-sm my-1" v-if="externalPending">获取信息中...</div>
              <Button size="sm" @click="addLink">提交</Button>
              <Button size="sm" class="ml-2" variant="secondary" @click="clearExternalUrl()">清空并关闭</Button>
            </div>
          </PopoverContent>
        </Popover>


        <Label for="imgUpload">
          <TooltipProvider>
            <Tooltip>
              <TooltipTrigger as-child>
                <Image :stroke-width="1.5" class="cursor-pointer w-[20px] h-[20px]" />
              </TooltipTrigger>
              <TooltipContent>
                <p>上传本地图片</p>
              </TooltipContent>
            </Tooltip>
          </TooltipProvider>

          <input type="file" id="imgUpload" class="hidden" name="file" @change="uploadImgs">
        </Label>

        <Popover :open="music163Open">
          <PopoverTrigger as="div">
            <TooltipProvider>
              <Tooltip>
                <TooltipTrigger as-child>
                  <Music4 :stroke-width="1.5" class="cursor-pointer w-[20px] h-[20px]" @click="music163Open = true" />
                </TooltipTrigger>
                <TooltipContent>
                  <p>嵌入网易云音乐</p>
                </TooltipContent>
              </Tooltip>
            </TooltipProvider>

          </PopoverTrigger>
          <PopoverContent as-child @interact-outside="music163Open = false">
            <div class="">
              <div class=" text-xs my-2 flex justify-between"><span>嵌入网易云音乐</span>
                <NuxtLink to="https://jerry.mblog.club/simple-moments-import-music-and-video"
                  class="text-gray-500 underline">
                  如何获取?</NuxtLink>
              </div>
              <Input class="my-2" placeholder="请输入网易云音乐代码" v-model="music163Url" />
              <Button size="sm" @click="importMusic">提交</Button>
            </div>
          </PopoverContent>
        </Popover>


        <Popover :open="bilibiliOpen">
          <PopoverTrigger as="div">
            <TooltipProvider>
              <Tooltip>
                <TooltipTrigger as-child>
                  <Youtube :stroke-width="1.5" class="cursor-pointer w-[20px] h-[20px]" @click="bilibiliOpen = true" />
                </TooltipTrigger>
                <TooltipContent>
                  <p>嵌入B站视频</p>
                </TooltipContent>
              </Tooltip>
            </TooltipProvider>

          </PopoverTrigger>
          <PopoverContent as-child @interact-outside="bilibiliOpen = false">
            <div class="">
              <div class=" text-xs my-2 flex justify-between"><span>嵌入B站视频</span>
                <NuxtLink to="https://jerry.mblog.club/simple-moments-import-music-and-video"
                  class="text-gray-500 underline">
                  如何获取?</NuxtLink>
              </div>
              <Input class="my-2" placeholder="请输入B站视频代码" v-model="bilibiliUrl" />
              <Button size="sm" @click="importBiliBili">提交</Button>
            </div>
          </PopoverContent>
        </Popover>
      </div>


      <div class="flex mx-auto gap-2">
        <TooltipProvider>
          <Tooltip>
            <TooltipTrigger as-child>
              <a href="/settings">
                <Settings :stroke-width="1.5" class="cursor-pointer w-[20px] h-[20px]" />
              </a>
            </TooltipTrigger>
            <TooltipContent>
              <p>进入设置</p>
            </TooltipContent>
          </Tooltip>
        </TooltipProvider>
        <TooltipProvider>
          <Tooltip>
            <TooltipTrigger as-child>
              <LogOut :stroke-width="1.5" class="cursor-pointer w-[20px] h-[20px]" @click="logout" />
            </TooltipTrigger>
            <TooltipContent>
              <p>退出</p>
            </TooltipContent>
          </Tooltip>
        </TooltipProvider>
      </div>


    </div>
    <div class="relative">
      <Textarea ref="textareaRef" @paste="pasteImg" autocomplete="new-text" v-model="content" rows="4"
        placeholder="今天发点什么呢?" class=" dark:text-[#C0BEBF]"></Textarea>
      <div class="absolute right-2 bottom-1 cursor-pointer text-xl" @click="toggleShowEmoji" ref="showEmojiRef">😊</div>
    </div>

    <Emoji v-if="showEmoji" class="mt-2" @emoji-selected="emojiSelected" />

    <iframe class="rounded" frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86
      :src="music163IfrUrl" v-if="music163IfrUrl"></iframe>

    <iframe class="w-full h-[250px] my-2" v-if="bilibiliIfrUrl" :src="bilibiliIfrUrl" scrolling="no" border="0"
      frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

    <div class="flex flex-row gap-2 my-2 bg-[#f7f7f7] dark:bg-[#212121] items-center p-2 border rounded"
      v-if="externalFavicon && externalTitle">
      <div class="flex-1 flex flex-row gap-2 items-center"><img class="w-8 h-8" :src="externalFavicon" alt="">
        <div class="text-sm text-[#576b95] cursor-pointer" v-if="!externalTitleEditing" title="点击编辑标题"
          @click="externalTitleEditing = true">{{ externalTitle }}</div>
        <Input placeholder="请输入链接标题" v-model="externalTitle" v-if="externalTitleEditing" />
      </div>
      <Check class="w-5 h-5 mr-2 cursor-pointer" color="green" v-if="externalTitleEditing"
        @click="externalTitleEditing = false" />
      <CircleX class="w-5 h-5 cursor-pointer" color="red" @click="clearExternalUrl" />
    </div>

    <div class="grid grid-cols-3 my-2 gap-2" v-if="imgs && imgs.length > 0">
      <div v-for="(img, index) in imgs" :key="index" class="relative">
        <img :src="getImgUrl(img)" class="rounded" />
        <Trash2 color="#379d1b" :size="15" class="absolute top-1 right-1 cursor-pointer"
          @click="imgs.splice(index, 1)" />
      </div>
    </div>
    <div class="flex flex-row justify-between mt-2 items-center gap-2 ">
      <div class="text-sm flex flex-row gap-1 flex-1 items-center">
        <Popover>
          <PopoverTrigger>
            <div class="text-[#576b95] text-sm cursor-pointer">{{ fmtLocation }}</div>
          </PopoverTrigger>
          <PopoverContent class="w-80">
            <div class="flex flex-row gap-2 text-sm">
              <Input v-model="location" placeholder="空格分割,火星都行!" />
              <Button variant="outline" @click="location = ''">清空</Button>
            </div>
          </PopoverContent>
        </Popover>
      </div>
      <Button @click="submitMemo">提交</Button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { getImgUrl, insertTextAtCursor } from '~/lib/utils';
import { Textarea } from '@/components/ui/textarea'
import { Button } from '@/components/ui/button'
import { toast } from 'vue-sonner'
import { memoUpdateEvent } from '@/lib/event'
import type { Memo } from '~/lib/types';
import { useAnimate } from '@vueuse/core';
import { Image, Music4, Settings, Trash2, LogOut, Youtube, Link, Loader, CircleX, Check, ShowerHead } from 'lucide-vue-next'

import {
  Tooltip,
  TooltipContent,
  TooltipProvider,
  TooltipTrigger
} from '@/components/ui/tooltip'
import {
  Popover,
  PopoverContent,
  PopoverTrigger,
} from '@/components/ui/popover'
const textareaRef = ref()
const showEmojiRef = ref<HTMLElement>()
const keyframes = { transform: 'rotate(360deg)' }
const showEmoji = ref(false)
const emit = defineEmits(['memoAdded'])
const toggleShowEmoji = () => {
  showEmoji.value = !showEmoji.value
  useAnimate(showEmojiRef.value, keyframes, { duration: 1000, easing: 'ease-in-out' })
}
const location = ref('')
const fmtLocation = computed(() => {
  if (location.value) {
    return location.value.split(' ').join(' · ')
  }
  return '自定义位置?'
})
const content = ref('')
const id = ref(-1)
const music163Url = ref('')
const music163IfrUrl = ref('')
const music163Open = ref(false)

const bilibiliUrl = ref('')
const bilibiliIfrUrl = ref('')
const bilibiliOpen = ref(false)

const linkOpen = ref(false)
const externalUrl = ref('')
const externalTitle = ref('')
const externalFavicon = ref('')
const externalPending = ref(false)
const externalFetchError = ref(false)
const externalTitleEditing = ref(false)

const clearExternalUrl = () => {
  externalUrl.value = ''
  externalTitle.value = ''
  externalFavicon.value = ''
  linkOpen.value = false
  externalFetchError.value = false
}
const addLink = async () => {
  if (externalFetchError.value && externalTitle.value === '') {
    toast.warning('请填写标题和图标')
    return
  }
  if (externalFetchError.value && externalTitle.value !== '') {
    externalFetchError.value = false
    linkOpen.value = false
    externalPending.value = false
    externalFavicon.value = externalFavicon.value || '/favicon.png'
    return
  }
  externalPending.value = true
  externalFetchError.value = false
  const { data: res } = await useAsyncData('external_' + externalUrl.value, async () => {
    return await $fetch('/api/memo/readExternal', {
      method: 'POST',
      body: JSON.stringify({ url: externalUrl.value })
    })
  })
  if (res.value?.success) {
    externalTitle.value = res.value?.title || '无法获取标题'
    externalFavicon.value = res.value?.favicon || '/favicon.png'
    linkOpen.value = false
    externalPending.value = false
  } else {
    toast.warning(res.value?.message || '获取失败')
    externalPending.value = false
    externalFetchError.value = true
  }
}

const imgs = ref<string[]>([])
const submitMemo = async () => {
  const res = await $fetch('/api/memo/save', {
    method: 'POST',
    body: JSON.stringify({
      id: id.value,
      content: content.value,
      imgUrls: imgs.value,
      music163Url: music163IfrUrl.value,
      bilibiliUrl: bilibiliIfrUrl.value,
      location: location.value,
      externalFavicon: externalFavicon.value,
      externalTitle: externalTitle.value,
      externalUrl: externalUrl.value
    })
  })
  if (res.success) {
    toast.success('提交成功')
    content.value = ''
    id.value = -1
    imgs.value = []
    music163IfrUrl.value = ''
    music163Url.value = ''
    bilibiliIfrUrl.value = ''
    bilibiliUrl.value = ''
    location.value = ''
    externalFavicon.value = ''
    externalTitle.value = ''
    externalUrl.value = ''
    showEmoji.value = false
    emit('memoAdded')
  } else {
    toast.warning('提交失败')
  }
}

const token = useCookie('token')
const logout = () => {
  token.value = ''
  navigateTo('/', { replace: true })
}


const pasteImg = async (event: ClipboardEvent) => {
  var items = event.clipboardData?.files
  if (!items || items.length === 0) {
    return;
  }
  await useUpload(items[0], async (res) => {
    if (res.success) {
      imgs.value = [...imgs.value, res.filename]
    } else {
      toast.warning(res.message || '上传失败')
    }
  })
}

const uploadImgs = async (event: Event) => {
  const file = (event.target as HTMLInputElement).files?.[0]
  if (!file) {
    return
  }

  await useUpload(file, async (res) => {
    if (res.success) {
      (event.target as HTMLInputElement).value = ''
      imgs.value = [...imgs.value, res.filename]
    } else {
      toast.warning(res.message || '上传失败')
    }
  })
}

const importMusic = () => {
  if (music163Url.value === '') {
    toast.warning('请输入网易云音乐代码')
    return
  }
  const match = music163Url.value.match(/src="(.*)&auto.*"/)
  if (match && match.length > 1) {
    const url = match[1]
    music163IfrUrl.value = url + '&auto=0&height=66'
    music163Open.value = false
  }
}


const importBiliBili = () => {
  if (bilibiliUrl.value === '') {
    toast.warning('请输入B站视频代码')
    return
  }
  const match = bilibiliUrl.value.match(/src="(.*?)"/)
  if (match && match.length > 1) {
    const url = match[1]
    bilibiliIfrUrl.value = url + '&autoplay=0&high_quality=1&as_wide=1'
    bilibiliOpen.value = false
  }
}



const emojiSelected = (emoji: string) => {
  const target = textareaRef.value?.getRef() as HTMLTextAreaElement
  insertTextAtCursor(emoji, target)
  content.value = target.value!
  // showEmoji.value = false
}
memoUpdateEvent.on((event: Memo) => {
  content.value = event.content
  id.value = event.id
  if (event.imgs) {
    imgs.value = event.imgs?.split(',')
  }
  location.value = event.location || ''
  externalFavicon.value = event.externalFavicon || ''
  externalTitle.value = event.externalTitle || ''
  externalUrl.value = event.externalUrl || ''
})
</script>

<style scoped></style>