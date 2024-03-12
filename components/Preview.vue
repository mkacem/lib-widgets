<template>
  <component 
    v-if="widget.component"
    :is="widget.component" 
    :data="properties" 
    :template="{}"
    :loadingData="false"
  />
</template>

<script setup>
const route = useRoute();
const widget = shallowRef({});
const properties = ref({});

watch(()=>route.query.organism, (to)=>{
  widget.value = useDynamicComponent(to);
});

const useDynamicComponent = (component: string)=> {
  let clones = useMap(component?.toLowerCase());
  const AsyncComp =  defineAsyncComponent( { 
    loader: async() => await import( `./org/${clones?clones.extend:component}.vue`).catch(
    (e) => { return import(`./Error.vue`)},
  ),      
  })
  return { component : AsyncComp, ...{componentProps:clones?.props}} 
}

const useMap = (component: any) => {
  // map cloned widget 
  let  k = null;
  Object.keys(widgets).forEach((key: any) => {
    let a = widgets[key].findIndex((w: any) =>w.name===component);
    if(a > -1){
      k = {extend :key, ...widgets[key][a]};
    }
  });
  return k;
};


watch(()=>route.query.properties, (to)=>{
  properties.value = to?populate(JSON.parse(transform(to))):{}
});
const mockValue = "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum";
const imgMock = "/pic.jpg"

onMounted(()=>{
  widget.value = useDynamicComponent(route.query.organism);
  properties.value = route.query.properties?populate(JSON.parse(transform(route.query.properties))):{}
  
});
const transform = (data) => {
  let r=data;
  r = r.replace(/("mediaType"\s*:\s*\"image\"\s*[\,a-zA-Z0-9\"\:\#\s]*\,"valueLight"\s*:)\s*\"\"/g,`$1"${imgMock}"`)
  r =  r.replace(/\"label\"\s*:\s*\"\"/g, `"label":"${mockValue}"`)
  return r;
}
const populate = (data) => {
  
  const recur = (e)=> {
    for (var key in e){
      if (key == "items" && e[key].length==1){
        e[key].push(...Array.from({length:3}, v => e[key][0]));
      } 
      if (typeof e[key] === 'object' && e[key] !== null) {
        recur(e[key]);     
      }
    } 
    return e;
  }  
  return recur(data)
}

</script>
