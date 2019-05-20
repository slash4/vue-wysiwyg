<template lang="pug">
div(@mousedown="onBtnClick" @keydown="onKey")
	a(:class="'vw-btn-'+module.title", v-html="module.icon")

	.dashboard(
		v-show="showDashboard",
		ref="dashboard"
	)
		component(
      v-if="module.render",
      v-once,
      ref="moduleDashboard",
      :is="module",
      @exec="exec",
			@savesel="savesel",
			@restoresel="restoresel",
      :uid="uid"
      :options="options"
    )

</template>
<script>
import bus from 'src/editor/bus.js';

export default {
	props: ["module", "options"],

	data () {
		return {
			showDashboard: false,
		}
	},

  computed: {
    uid () {
      return this.$parent._uid
    }
  },

	methods: {
		closeDashboard () {
			this.showDashboard = false;
		},

		openDashboard () {
			this.showDashboard = true;
		},

    exec () {
      this.$parent.exec.apply(null, arguments)
		},

		savesel () {
      this.$parent.saveSelection()
		},

		restoresel () {
      this.$parent.restoreSelection()
		},

		onKey ($event) {
			console.log($event);
		},

		onBtnClick ($event) {

			if($event.target.localName == 'input'){
				console.log($event);
				return;
			}
			$event.preventDefault();




			if (this.module.action !== undefined)
				this.exec.apply(null, this.module.action);

			else if (this.module.customAction !== undefined) {
				this.module.customAction(bus.utils).forEach(a => this.exec.apply(null, a));
			}

			else if (
				this.module.render !== undefined &&
				(!this.$refs.dashboard || !this.$refs.dashboard.contains($event.target))
			) {
				this.showDashboard = !this.showDashboard;
				bus.emit(`${this.uid}_${this.showDashboard ? "show" : "hide"}_dashboard_${this.module.title}`);
				return;
			}
		}
	}
}
</script>
