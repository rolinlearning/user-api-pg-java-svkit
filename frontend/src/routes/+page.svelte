<script lang="ts">
    import { onMount } from 'svelte';
    import { writable } from 'svelte/store';
  
    interface User {
      id: number;
      name: string;
      email: string;
      password: string;
    }
  
    const users = writable<User[]>([]);
    let name = '';
    let email = '';
    let password = '';
  
    async function fetchUsers() {
      const res = await fetch('http://localhost:8080/api/users');
      const data = await res.json();
      users.set(data);
    }
  
    async function addUser() {
      const newUser = { name, email, password };
      const res = await fetch('http://localhost:8080/api/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(newUser),
      });
      if (res.ok) {
        const created = await res.json();
        users.update(u => [created, ...u]);
        name = email = password = ''; // Clear form
      }
    }
  
    onMount(fetchUsers);
  </script>
  
  <div class="min-h-screen bg-gray-900 text-white flex flex-col items-center p-8 space-y-6">
    <h1 class="text-3xl font-bold">Users</h1>
  
    <div class="space-y-4 w-full max-w-md">
      <input type="text" placeholder="Name" bind:value={name} class="w-full rounded bg-gray-800 p-2" />
      <input type="email" placeholder="Email" bind:value={email} class="w-full rounded bg-gray-800 p-2" />
      <input type="password" placeholder="Password" bind:value={password} class="w-full rounded bg-gray-800 p-2" />
      <button on:click={addUser} class="w-full bg-indigo-600 hover:bg-indigo-500 rounded p-2 font-semibold">
        Add User
      </button>
    </div>
  
    <table class="w-full max-w-4xl mt-8 table-auto border-collapse border border-gray-700">
      <thead>
        <tr class="bg-gray-800">
          <th class="border border-gray-700 p-2">ID</th>
          <th class="border border-gray-700 p-2">Name</th>
          <th class="border border-gray-700 p-2">Email</th>
        </tr>
      </thead>
      <tbody>
        {#each $users as user}
          <tr class="hover:bg-gray-700">
            <td class="border border-gray-700 p-2">{user.id}</td>
            <td class="border border-gray-700 p-2">{user.name}</td>
            <td class="border border-gray-700 p-2">{user.email}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
  