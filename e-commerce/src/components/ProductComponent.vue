<script>
export default {
  name: "GroupProducts",
  methods: {
  finalPrice(pro) {
    if (typeof pro.promotionAsPercentage === "number" && pro.promotionAsPercentage > 0) {
      return (pro.price - (pro.price * pro.promotionAsPercentage) / 100).toFixed(2);
    }
    return pro.price.toFixed(2);
  },
  star(rating) {
    const nostar = 5 - rating;
    if(rating<5){
      return "⭐".repeat(rating)+"☆".repeat(nostar);
    }else if(rating==5){
      return "⭐".repeat(rating);
    }

  return "⭐".repeat(rating);
  },
    extract_img(input) {
    if (!input) return "";  

    try {
 
      const arr = JSON.parse(input);
      return Array.isArray(arr) && arr.length > 0 ? arr[0] : "";
    } catch (e) {
 
      return input.replace('["', "").replace('"]', "").split('","')[0] || "";
    }
  },

  shopnow(pro){
    alert("Product "+pro.name+" add to cart");
  }


},

  props: {
    product: Array
  },
};
</script>

<template>
  <div class="grid xl:grid-cols-5 gap-4 p-4">
    <div 
      class="bg-white rounded-lg p-2 flex flex-col transition-all duration-300 border-b border-gray-100 relative"
      v-for="(pro, index) in product"
      :key="index"
    >
      <div 
        v-if="typeof pro.promotionAsPercentage === 'number' && pro.promotionAsPercentage > 0" 
        class="absolute top-2 left-2 bg-[#3BB77E] text-white text-xs font-bold px-3 py-1 rounded-md z-20"
      >
        -{{ pro.promotionAsPercentage }}%
      </div>
      <div 
        v-else-if="pro.promotionAsPercentage === 'Hot'" 
        class="absolute top-2 left-2 bg-red-500 text-white text-xs font-bold px-3 py-1 rounded-md z-20"
      >
        Hot
      </div>
      <div 
        v-else-if="pro.promotionAsPercentage === 'Sale'" 
        class="absolute top-2 left-2 bg-yellow-500 text-gray-800 text-xs font-bold px-3 py-1 rounded-md z-20"
      >
        Sale
      </div>
      <div class="w-full h-40 object-cover mx-auto mb-2">
        <img 
          :src="`http://localhost:3000/${extract_img(pro.image)}`" 
          :alt="pro.name" 
          class="w-full h-full object-contain"
        >        
      </div>
      
      <div class="px-2 pb-2">  
        <div class="text-sm text-gray-500 mb-1">
          <h4>{{ pro.group || 'Hodo Foods' }}</h4>
        </div>
        
        <div class="text-gray-800 text-sm font-semibold mb-1 h-10 overflow-hidden"> 
          <h2>{{ pro.name }}</h2>
        </div>
        
        <div class="flex items-center text-xs mb-2">
          <span class="mr-1">{{ star(pro.rating) }}</span>
          <span class="text-gray-500">({{ pro.rating }})</span>
        </div>
        
        <div class="text-xs text-gray-500 mb-2">
            {{ pro.size || '500 gram' }}
        </div>
        
        <div class="flex items-center justify-between mt-2">
          
          <div class="flex items-end">
            <span class="text-lg font-bold text-green-600">${{ finalPrice(pro) }}</span>
            <span class="text-sm line-through text-gray-400 ml-1">$2.80</span>
          </div>

          <div v-if="index === 0">
            <input 
              type="number" 
              value="1" 
              min="1"
              class="border border-green-400 rounded-md text-center text-sm w-16 h-8 p-1 focus:border-green-500 focus:ring-1 focus:ring-green-500"
              style="
                -moz-appearance: textfield; 
                appearance: textfield;
              "
              aria-label="Quantity"
              @change="shopnow(pro)"
            />
          </div>
          
          <div 
            v-else 
            class="flex items-center justify-center bg-[#DEF9EC] rounded-md h-8 w-16 cursor-pointer transition-colors duration-200 hover:bg-[#bce3c9]"
            @click="shopnow(pro)"
          >
            <span class="text-sm font-semibold text-green-600">
              Add +
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>

input[type=number]::-webkit-inner-spin-button, 
input[type=number]::-webkit-outer-spin-button { 
  opacity: 1; 
  margin: 0;
}

input[type=number]:focus {
    box-shadow: none;
    border-color: initial;
}

.counter-container button {
  cursor: pointer;
  transition: background-color 0.2s;
}
.counter-container button:active {
  background-color: #e0e0e0;
}
</style>