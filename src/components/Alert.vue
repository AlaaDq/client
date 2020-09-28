<template>
  <div class="row alert alert-success" >
    <p>
        <slot></slot>
        <a href="#" style="float:right" @click="$emit('hide')">x</a>
    </p>
  </div>
</template>

<script>
export default {};
</script>

<script>
    export default {};
</script>

<style  scoped>
a{
    position: absolute;
    right: 10%;
    font-size: x-large;
    text-decoration: none;
}
.row{
    position: relative;
}
</style>
