<template>
  <div class="bg-gray-100 min-h-screen">
    <!-- Navigation -->
    <nav class="bg-gray-800 p-4 text-white">
      <div class="container mx-auto flex justify-between items-center">
        <h1 class="text-3xl font-bold">Api Docs</h1>
        <NuxtLink to="/" class="text-xl font-bold">Main Page</NuxtLink>
      </div>
    </nav>

    <div class="container mx-auto p-6 mt-8">
      <div class="mb-8">
        <h2 class="text-2xl font-bold mb-2 text-blue-600">
          "Get method" for all todos
        </h2>
        <p class="text-gray-700">http://localhost:8080/api/todos</p>
      </div>

      <div class="mb-8">
        <h2 class="text-2xl font-bold mb-2 text-green-600">
          "Get method" for one todo with id
        </h2>
        <p class="text-gray-700">http://localhost:8080/api/todos/:id</p>
      </div>

      <div class="mb-8">
        <h2 class="text-2xl font-bold mb-2 text-yellow-600">
          "Post method" for create todo
        </h2>
        <p class="text-gray-700">http://localhost:8080/api/todos</p>
      </div>

      <div class="mb-8">
        <h2 class="text-2xl font-bold mb-2 text-yellow-600">
          "Put method" for update one todo with id
        </h2>
        <p class="text-gray-700">http://localhost:8080/api/todos/:id</p>
      </div>

      <div class="mb-8">
        <h2 class="text-2xl font-bold mb-2 text-red-600">
          "Delete method" for delete one todo with id
        </h2>
        <p class="text-gray-700">http://localhost:8080/api/todos/:id</p>
      </div>
    </div>
  </div>
</template>