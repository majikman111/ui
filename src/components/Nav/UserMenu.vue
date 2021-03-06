<script>
import { mapActions, mapGetters } from 'vuex'
import { formatTime } from '@/mixins/formatTimeMixin'

export default {
  mixins: [formatTime],
  data() {
    return {
      clockInterval: null,
      model: false,
      routes: [
        {
          title: 'Profile',
          subtitle: 'Some details about what profile is',
          route: {
            name: 'profile'
          }
        },
        {
          title: 'Tokens',
          subtitle: 'Some details about what user tokens are',
          route: {
            name: 'user-tokens'
          }
        }
      ],
      time: Date.now()
    }
  },
  computed: {
    ...mapGetters('auth', ['isAuthorized']),
    ...mapGetters('user', ['user', 'oktaUser'])
  },
  mounted() {
    clearInterval(this.clock)
    this.clockInterval = setInterval(() => {
      this.time = Date.now()
    }, 1000)
  },
  methods: {
    ...mapActions('auth', ['logout']),
    async wipeClientAndLogout() {
      document.querySelector('.router-view').style.opacity = 0
      await this.logout(this.$apolloProvider.clients.defaultClient)
    }
  }
}
</script>

<template>
  <v-menu
    v-model="model"
    offset-y
    data-cy="nav-bar-menu"
    transition="slide-y-transition"
    :close-on-content-click="false"
    nudge-bottom="15"
  >
    <template #activator="{ on }">
      <v-scale-transition>
        <v-btn
          class="white ml-3 mr-1"
          large
          icon
          title="Open the user menu"
          v-on="on"
        >
          <v-icon large color="grey">
            face
          </v-icon>
        </v-btn>
      </v-scale-transition>
    </template>

    <v-sheet width="300" class="pt-6 text-center">
      <div class="text-center">
        <v-avatar size="64" :tile="!oktaUser.picture">
          <img
            :src="
              oktaUser.picture ||
                require('@/assets/logos/logomark-cerulean.svg')
            "
            :alt="oktaUser.name"
          />
        </v-avatar>

        <div class="mt-2 text-h6">
          {{ user.first_name }} {{ user.last_name }}
        </div>
        <div class="caption"> {{ user.email }} </div>
      </div>

      <div
        v-ripple
        class="mx-auto my-4 py-2 px-6 text-center rounded-lg d-inline-block cursor-pointer"
        style="border: 2px solid #eee;"
        @click="$router.push({ name: 'profile' })"
      >
        <div>
          <v-icon x-small>
            fad fa-cogs
          </v-icon>
          Account Settings
        </div>
        <div class="text-caption text--secondary text-truncate">
          Manage profile, access tokens, and teams
        </div>
      </div>

      <v-divider class="grey lighten-3 mx-auto my-2" style="width: 50%;" />

      <div class="my-4 text-h4 text-center primary--text">
        {{ datePartHour(time) }}<span class="black--text">:</span
        >{{ datePartMinute(time) }}
        <span class="text-overline black--text">
          {{ datePartMeridian(time) }}
        </span>

        <v-tooltip top>
          <template #activator="{ on }">
            <v-icon class="material-icons-outlined" x-small v-on="on">
              info
            </v-icon>
          </template>
          System time according to your set Timezone; you can change this from
          your Account Settings.
        </v-tooltip>
      </div>

      <!-- <v-divider class="grey lighten-3 mx-auto my-2" style="width: 50%;" /> -->

      <div>
        <!-- <MenuLink
          v-for="r in routes"
          :key="r.title"
          :primary="r.title"
          :secondary="r.subtitle"
          :route="r.route"
        /> -->

        <v-btn
          class="text-capitalize py-6"
          depressed
          block
          @click="wipeClientAndLogout"
        >
          <span class="mr-2">Sign out</span>
          <i class="fad fa-sign-out" />
        </v-btn>
      </div>
    </v-sheet>
  </v-menu>
</template>
