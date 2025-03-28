<script>
  export let pokemon;
  export let onClose;

  if (!pokemon) {
    onClose(); // Si no hay datos, cerramos el modal automáticamente
  }
</script>

<div class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50">
  <div class="bg-white p-6 rounded-lg shadow-lg max-w-md w-full">
    <button class="absolute top-2 right-2 text-gray-700" on:click={onClose}>✖</button>
    <h2 class="text-2xl font-bold text-center mb-4">{pokemon.name}</h2>
    
    <img class="w-32 h-32 mx-auto" src={pokemon.sprites?.other?.['official-artwork']?.front_default || 'https://via.placeholder.com/150'} alt={pokemon.name} />
    
    <div class="mt-4">
      <p><strong>Altura:</strong> {pokemon.height / 10} m</p>
      <p><strong>Peso:</strong> {pokemon.weight / 10} kg</p>
    </div>

    <div class="mt-4">
      <p><strong>Tipos:</strong></p>
      <div class="flex space-x-2">
        {#each pokemon.types as type}
          <span class="px-2 py-1 bg-gray-300 rounded">{type.type.name}</span>
        {/each}
      </div>
    </div>
    
    <button class="mt-4 w-full bg-red-500 text-white py-2 rounded" on:click={onClose}>Cerrar</button>
  </div>
</div>
