<template>
    <div class="container">
        <div class="row">
            <div class="col-sm-12 text-center">
                <div class="progress">
                    <div class="progress-bar " role="progressbar" aria-valuenow="0" aria-valuemin="0" aria-valuemax="100" :style="{width: (timeLeft * 10) + '%'}"></div>
                </div>
            </div>
        </div>
    </div>
    </div>
</template>

<script>
export default {
  props: ['timeLeft']
}
</script>

<style scoped>
    .progress {
        width: 50%;
        margin: 0 auto;
        height: 7px;
    }
    .progress-bar {
        background-color: rgb(122,181,96)
    }
</style>
