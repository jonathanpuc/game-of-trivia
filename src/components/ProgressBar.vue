<template>
    <div class="container">
        <div class="row">
            <div class="col-sm-12 text-center">
                <div class="progress">
                    <div class="progress-bar bg-danger progress-bar-striped" role="progressbar" aria-valuenow="0" aria-valuemin="0" aria-valuemax="10" :style="{width: playerHP + '%'}"></div>
                </div>
            </div>
        </div>
    </div>
    </div>
</template>

<script>
export default {
  props: ['playerHP']
}
</script>

<style scoped>
    .container {
        margin-top: 30px;
    }
    .progress {
        width: 90%;
        margin: auto;
        height: 12px;
    }
    .progress-bar {
        background-color: rgb(138, 7, 7);
    }
</style>
