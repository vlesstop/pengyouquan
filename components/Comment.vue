<template>
  <div class="relative select-none">

    <div class="dark:text-[#9F9F9F] ">
      <span class="text-[#576b95] text-nowrap">{{ comment.username ?? '匿名' }}
        <b v-if="comment.author" class="border text-xs border-[#C64A4A] rounded px-0.5 text-[#C64A4A]">作者</b></span>
      <span v-if="comment.replyTo" class="text-nowrap mx-1">回复<span class="text-[#576b95] ml-1">{{
        comment.replyTo }}</span> </span>
      <span class="mr-0.5">:</span>
      <span :title="`点击回复${comment.username}`" class="inline w-full break-all cursor-pointer"
        @click="toggleUserComment">{{
          comment.content }}</span>
      <AlertDialog v-if="token">
        <AlertDialogTrigger asChild>
          <Trash2 :size=14 class="align-text-top	ml-2 cursor-pointer inline-block text-gray-300" />
        </AlertDialogTrigger>
        <AlertDialogContent>
          <AlertDialogHeader>
            <AlertDialogTitle>确定删除评论吗?</AlertDialogTitle>
            <AlertDialogDescription>
              <div><span class="font-bold">评论内容</span>:</div>
              <div>{{ comment.content }}</div>
              <div class="my-2 text-red-400">无法恢复,你确定删除吗?</div>
            </AlertDialogDescription>
          </AlertDialogHeader>
          <AlertDialogFooter>
            <AlertDialogCancel>取消</AlertDialogCancel>
            <AlertDialogAction @click="removeComment">确定</AlertDialogAction>
          </AlertDialogFooter>
        </AlertDialogContent>
      </AlertDialog>
    </div>
    <FriendsCommentInput @commentAdded="refreshComment" :memoId="comment.memoId" :commentId="comment.id"
      :reply="comment.username" v-if="showCommentInput" />
  </div>
</template>

<script setup lang="ts">
import type { Comment } from '@/lib/types';
import { Trash2 } from 'lucide-vue-next'
import { toast } from 'vue-sonner';


const emit = defineEmits(['memo-update'])

const token = useCookie('token')
const showCommentInput = ref(false)
const props = withDefaults(
  defineProps<{
    comment: Comment
  }>(), {}
)

const refreshComment = async () => {
  emit('memo-update')  
  showCommentInput.value = false
}

const toggleUserComment = () => {
  showCommentInput.value = !showCommentInput.value
}

const removeComment = async () => {
  const res = await $fetch('/api/comment/remove', {
    method: 'POST',
    body: JSON.stringify({
      commentId: props.comment.id
    })
  })
  if (res.success) {
    toast.success('删除成功')
    emit('memo-update')
  }
}

</script>

<style scoped></style>