<template>
  <div class="mt-1 mb-4">
    <div class="row">
      <div class="col-6 offset-1">
        <datasetDetailsNavigation></datasetDetailsNavigation>
      </div>
      <div class="col-4 text-right">
        <datasetDetailsFeedbackButton></datasetDetailsFeedbackButton>
        <div class="d-inline dropdown">
          <a class="mt-1 btn btn-sm btn-light dropdown-toggle" href="#" role="button" id="dropdownMenuLink" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            <!--<i class="material-icons small-icon align-bottom text-dark">share</i>-->
            Share Dataset
          </a>
          <div class="dropdown-menu" aria-labelledby="dropdownMenuLink">
            <datasetDetailsShareButton class="dropdown-item" :to="`https://www.facebook.com/sharer.php?u=${url}`" :icon="{ prefix: 'fab', iconName: 'facebook-f' }"></datasetDetailsShareButton>
            <datasetDetailsShareButton class="dropdown-item" :to="`https://twitter.com/intent/tweet?url=${url}`" :icon="{ prefix: 'fab', iconName: 'twitter' }"></datasetDetailsShareButton>
            <datasetDetailsShareButton class="dropdown-item" :to="`https://www.linkedin.com/shareArticle?mini=true&url=${url}`" :icon="{ prefix: 'fab', iconName: 'linkedin-in' }"></datasetDetailsShareButton>
          </div>
        </div>
      </div>
      <div class="col-12 text-right" v-if="authenticated">
        <datasetDetailsEditButton></datasetDetailsEditButton>
        <datasetDetailsDeleteButton></datasetDetailsDeleteButton>
      </div>
      <div class="col-12 mt-3">
        <datasetDetailsHeader></datasetDetailsHeader>
      </div>
    </div>
  </div>
</template>


<script>
import { mapGetters, mapActions } from 'vuex';
import DatasetDetailsNavigation from './EDP2-datasetDetails-navigation';
import DatasetDetailsFeedbackButton from './EDP2-datasetDetails-feedbackButton';
import DatasetDetailsShareButton from './EDP2-datasetDetails-shareButton';
import DatasetDetailsHeader from './EDP2-datasetDetails-header';
import DatasetDetailsDeleteButton from './EDP2-datasetDetails-deleteButton';
import DatasetDetailsEditButton from './EDP2-datasetDetails-editButton';
import AppLink from './AppLink';
import { decode } from '../utils/jwt';

export default {
  name: 'datasetDetailsTop',
  dependencies: ['authService', 'DatasetService'],
  components: {
    datasetDetailsNavigation: DatasetDetailsNavigation,
    datasetDetailsFeedbackButton: DatasetDetailsFeedbackButton,
    datasetDetailsShareButton: DatasetDetailsShareButton,
    datasetDetailsDeleteButton: DatasetDetailsDeleteButton,
    datasetDetailsEditButton: DatasetDetailsEditButton,
    datasetDetailsHeader: DatasetDetailsHeader,
    appLink: AppLink,
  },
  data() {
    return {
      rtpToken: '',
      catalogId: '',
    };
  },
  computed: {
    // import store-getters
    ...mapGetters('auth', [
      'securityAuth',
      'getRTPToken',
    ]),
    ...mapGetters('datasetDetails', [
      'getCatalog',
    ]),
    // Returns true, if the authenticated user has update permissions on this particular dataset
    authenticated() {
      if (!this.getRTPToken) return false;
      if (!this.getCatalog.id) return false;

      const decodedRtpToken = this.decode(this.getRTPToken);

      const hasUpdatePermissions = decodedRtpToken.authorization.permissions.find((permission) => {
        if (this.getCatalog.id === permission.rsname.replace('https://europeandataportal.eu/id/catalogue/', '')) {
          return permission.scopes.find(scope => scope === 'update');
        }
        return false;
      });

      return !!hasUpdatePermissions;
    },
    url() { return window.location.href; },
  },
  created() { },
  props: {
    activeTab: {
      type: Number,
      default: 0,
    },
  },
  methods: {
    // import store-actions
    ...mapActions('datasetDetails', [
      'loadDatasetDetails',
      'useService',
    ]),
    decode,
  },
};
</script>

<style lang="scss" scoped>
  @import '../styles/bootstrap_theme';

  /*** MATERIAL ICONS ***/
  .material-icons.small-icon {
    font-size: 20px;
  }
</style>
