<template>
  <br><br><br>
  <div class="mt-5 mb-5"></div>
  <div class="flex flex-col md:flex-row">
    <div class="w-6/12 md:mr-4 md:ml-4 mx-auto">
      <img :src="item.image" alt="de la imagen" class="w-full h-auto">
    </div>
    <section class="md:w-[1040px] mr-4">
      <div
        class="inline-flex items-center justify-center text-sm font-medium text-white bg-secondary-600 py-1.5 px-3 mb-4">
        <SfIconSell size="sm" class="mr-1.5" />
        {{ item.status ? 'A la venta' : 'Agotado' }}
      </div>
      <h1 class="mb-1 font-bold typography-headline-4 text-3xl">{{ item.name }}</h1>
      <ul class="mb-4 font-normal typography-text-sm text-xl">
        <li>{{ item.description }} </li>
      </ul>
      <strong class="block font-bold typography-headline-3 text-xl">{{ item.price }}</strong>
      <div class="py-4 mb-4 border-gray-200 border-y">
        <div class="items-start xs:flex">
          <div class="flex flex-col items-stretch xs:items-center xs:inline-flex">
            <div class="flex border border-neutral-300 rounded-md">
              <SfButton variant="tertiary" :disabled="count <= min" square class="rounded-r-none p-3"
                :aria-controls="inputId" aria-label="Decrease value" @click="dec()">
                <SfIconRemove />
              </SfButton>
              <input :id="inputId" v-model="count" type="number"
                class="grow appearance-none mx-2 w-8 text-center bg-transparent font-medium [&::-webkit-inner-spin-button]:appearance-none [&::-webkit-inner-spin-button]:display-none [&::-webkit-inner-spin-button]:m-0 [&::-webkit-outer-spin-button]:display-none [&::-webkit-outer-spin-button]:m-0 [-moz-appearance:textfield] [&::-webkit-outer-spin-button]:appearance-none disabled:placeholder-disabled-900 focus-visible:outline focus-visible:outline-offset focus-visible:rounded-sm"
                :min="min" :max="max" @input="handleOnChange" />
              <SfButton variant="tertiary" :disabled="count >= max" square class="rounded-l-none p-3"
                :aria-controls="inputId" aria-label="Increase value" @click="inc()">
                <SfIconAdd />
              </SfButton>
            </div>
            <p class="self-center mt-1 mb-4 text-xs text-neutral-500 xs:mb-0">
              <strong class="text-neutral-900">{{ item.stock }}</strong> en stock
            </p>
          </div>

          <SfButton size="lg" class="w-full xs:ml-4" @click="sendWhatssapMessage()">
            <template #prefix>
              <SfIconShoppingCart size="sm" />
            </template>
            Pedir producto
          </SfButton>
        </div>
      </div>
      <div class="flex first:mt-4">
        <SfIconPackage size="sm" class="flex-shrink-0 mr-1 text-neutral-500" />
        <p class="text-sm">
          No encuentras el producto que quieres?
          <SfLink href="#" variant="secondary" class="mx-1" @click="sendWhatssapMessageConsulta()"> Envianos un mensaje
          </SfLink>
          para ayudar a econtrarlo
        </p>
      </div>
      <div class="flex mt-4">
        <SfIconWarehouse size="sm" class="flex-shrink-0 mr-1 text-neutral-500" />
        <p class="text-sm">
          No sabes donde recoger tus productos
          <SfLink href="#" variant="secondary" class="ml-1" @click="paginaUbicacion()"> Te mostramos nuestras sucursales
          </SfLink>
        </p>
      </div>
      <div class="flex mt-4">
        <SfIconSafetyCheck size="sm" class="flex-shrink-0 mr-1 text-neutral-500" />
        <p class="text-sm">
          ¿No estas satisfecho con nuestro servicio?
          <SfLink href="#" variant="secondary" class="ml-1"> Contactanos </SfLink>
        </p>
      </div>
    </section>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue';
import {
  SfButton,
  SfCounter,
  SfLink,
  SfRating,
  SfIconSafetyCheck,
  SfIconCompareArrows,
  SfIconWarehouse,
  SfIconPackage,
  SfIconFavorite,
  SfIconSell,
  SfIconShoppingCart,
  SfIconAdd,
  SfIconRemove,
  useId,
  SfIconShoppingCartCheckout,
} from '@storefront-ui/vue';
import { clamp } from '@storefront-ui/shared';
import { useCounter } from '@vueuse/core';
import { useRoute } from 'vue-router';
//catalogo
import { getOneProduct, getProduct } from "../../../api/product.js"


const inputId = useId();
const min = ref(1);
const max = ref(999);
const { count, inc, dec, set } = useCounter(1, { min: min.value, max: max.value });
function handleOnChange(event: Event) {
  const currentValue = (event.target as HTMLInputElement)?.value;
  const nextValue = parseFloat(currentValue);
  set(clamp(nextValue, min.value, max.value));
}
//cargar Producto
const producto = ref(null);
const productId = ref(null);
const route = useRoute();
//catalogo
const item = ref([]);

async function loadData() {
  try {
    //console.log(productId.value);

    const res = await getOneProduct(productId.value);
    item.value = res.data.product;
    // Filtrar los productos según el término de búsqueda actual
  } catch (error) {
    console.error("Error al cargar datos", error);
  }
}


const cargarProducto = () => {
  // Obtén el ID del producto de los parámetros de la ruta
  productId.value = route.params.id;
  //console.log(productId);
  // Aquí, simplemente se simula un objeto de producto.
  const mockProduct = {
    id: productId,
    nombre: 'Nombre del Producto',
    descripcion: 'Descripción del Producto',
    // ... otras propiedades del producto
  };

  // Asigna la información del producto a la variable reactiva
  producto.value = mockProduct;
};
// Llama al método cargarProducto cuando se monta el componente
//onMounted(cargarProducto);
onMounted(() => {
  cargarProducto();
  loadData();
});
//Mandar mensaje
const sendWhatssapMessage = () => {
  const numeroTelefono = '+591 62426763'; // Reemplaza con el número de teléfono de destino (debe estar en formato internacional)
  const mensaje = '¡Hola, quisiera pedir ' + count.value + ' ' + item.value.name + '\nNombre:' + '\nCI:'; // Mensaje personalizado
  // Genera el enlace de WhatsApp
  const linkWhatsApp = `https://api.whatsapp.com/send?phone=${encodeURIComponent(numeroTelefono)}&text=${encodeURIComponent(mensaje)}`;
  // Abre el enlace en una nueva ventana o pestaña
  window.open(linkWhatsApp, '_blank');
};
//Mandar mensaje consulta
const sendWhatssapMessageConsulta = () => {
  const numeroTelefono = '+591 62426763'; // Reemplaza con el número de teléfono de destino (debe estar en formato internacional)
  const mensaje = '¡Hola, quisiera saber si tiene el siguiente producto: ';   // Genera el enlace de WhatsApp
  const linkWhatsApp = `https://api.whatsapp.com/send?phone=${encodeURIComponent(numeroTelefono)}&text=${encodeURIComponent(mensaje)}`;
  // Abre el enlace en una nueva ventana o pestaña
  window.open(linkWhatsApp, '_blank');
};
//Mandar mensaje atencion al cliente
const sendWhatssapMessageAtencion = () => {
  const numeroTelefono = '+591 62426763'; // Reemplaza con el número de teléfono de destino (debe estar en formato internacional)
  const mensaje = '¡Hola, tengo una consulta o queja: ';   // Genera el enlace de WhatsApp
  const linkWhatsApp = `https://api.whatsapp.com/send?phone=${encodeURIComponent(numeroTelefono)}&text=${encodeURIComponent(mensaje)}`;
  // Abre el enlace en una nueva ventana o pestaña
  window.open(linkWhatsApp, '_blank');
};

const paginaUbicacion = () => {
  const link = `http://localhost:5173/aboutus#personal`;
  // Abre el enlace en una nueva ventana o pestaña
  window.open(link, '_blank');
};
</script>
