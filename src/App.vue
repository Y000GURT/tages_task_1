<template>

</template>

<script setup>
import { onMounted } from 'vue'

let users
let posts

async function fetchData() { // Получение пользователей и постов
  try {
    let responseUsers = await fetch('http://jsonplaceholder.typicode.com/users')
    let responsePosts = await fetch('http://jsonplaceholder.typicode.com/posts')

    users = await responseUsers.json()
    posts = await responsePosts.json()

  } catch(error) {
    alert('Error users and posts: ' + error)
  }
}
async function getComments(id) { // получение комментариев 
  try {
    let response = await fetch(`http://jsonplaceholder.typicode.com/posts/${id}/comments`)

    let com = await response.json()
    return com
  } 
  catch(error) {
    alert('Error comments: ' + error)
  }
}

async function createPosts(id) { // создание массива с постами

  let finalPosts = []

  for(let post of posts) {

    if (post.userId === id) {
      finalPosts.push({
        id: post.id,
        title: post.title,
        title_crop: post.title.length > 20 ? post.title.slice(0, 20) + '...' : post.title,
        body: post.body,
        comments: post.userId === 2 ? await getComments(post.id) : null,
      })
    }
  }
  return finalPosts
}

async function createFinalArray() { // создание итогового результата

  let finalArray = []

  for(let user of users) {

    finalArray.push({
      id: user.id,
      name: user.name,
      email: user.email,
      address: user.address.city + ', ' + user.address.street + ', ' + user.address.suite,
      website: 'https://' + user.website,
      company: user.company.name,
      posts: await createPosts(user.id),
    })
  }

  console.log(finalArray)
}

onMounted(async () => {
  await fetchData()
  createFinalArray()
})
</script>

<style>

</style>
