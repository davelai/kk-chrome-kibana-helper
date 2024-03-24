<template>
  <div>
    <el-form v-model="form">
      <el-form-item label="log_label: RESPONSE">
        <el-switch v-model="form.ifLogLabelResponse"></el-switch>
      </el-form-item>
      <el-form-item label="log_type: kkday-b2b-api">
        <el-switch v-model="form.ifLogTypeB2BAPI"></el-switch>
      </el-form-item>
      <el-form-item label="log_type: message: b2bOrder">
        <el-switch v-model="form.ifMessageB2Border"></el-switch>
      </el-form-item>
      <el-form-item label="custom_kkday-b2b-api.serviceName:">
        <el-select v-model="form.b2bServiceName" filterable allow-create clearable>
          <el-option
              v-for="service in services"
              :key="service"
              :label="service"
              :value="service">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="custom_kkday-b2b-api.orderMid:">
        <el-input v-model="form.b2bServiceOrderMid"></el-input>
      </el-form-item>
      <el-form-item label="custom_kkday-b2b-api.searchDate">
        <el-select v-model="form.searchDate" filterable clearable>
          <el-option value="5min" label="in 5 minutes"></el-option>
          <el-option value="1hour" label="in 1 hour"></el-option>
          <el-option value="1day" label="in 1 day"></el-option>
          <el-option value="1week" label="in 1 week"></el-option>
          <el-option value="1month" label="in 1 month"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="search">Search</el-button>
        <el-button @click="openUuid">Open Uuid</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        ifLogLabelResponse: true,
        ifLogTypeB2BAPI: true,
        ifMessageB2Border: false,
        b2bServiceName: '---',
        b2bServiceOrderMid: '',
        searchDate: '1day',
      },
      services: ['---', 'ACG', 'AEL', 'AX', 'Airalo', 'B2BProduct', 'B2bOrder', 'B2rMedia', 'B2rMultiLang', 'B2rPackage', 'B2rProduct', 'BMGBooking', 'BMGData', 'BMGProductOption', 'BMGProduct', 'BMGProductService_old.php', 'BMG', 'Bcifop', 'BookingRule', 'Broadway', 'CKS', 'CalendarRule', 'Chimelong', 'CityTour', 'Coreworks', 'Ctrip', 'CustomLinc', 'DJB', 'E7Life', 'ERL', 'Edenred', 'Enjoyaus', 'Everland', 'ExcitingPenghu', 'ExperienceOz', 'Ezo', 'Fake', 'FargloryOceanPark', 'Fontrip', 'FunNow', 'GalaxyConnect', 'GlobalTix', 'GoodFellows', 'HIS', 'HKDL', 'HKOP', 'HKOPv2', 'HS', 'HanaTour', 'ITC', 'IVenture', 'Ibis', 'Ingresso', 'Inventory', 'JPH', 'JPHv2', 'JRKyushu', 'JejuMobile', 'JejuShinhwaWorld', 'K11', 'KKApi', 'LPG', 'LSCompany', 'Lihpao', 'Linktivity', 'LongHung', 'M12', 'MacauTower', 'MelcoCrown', 'Mohist', 'NP360', 'Nanta', 'Nextstory', 'Ocard', 'PeakTramways', 'Phantom', 'Playstory', 'PlusN', 'Prepia', 'REZIO', 'RWC', 'RWS', 'RailEurope', 'RedTable', 'Redeam', 'SANPU', 'SET', 'SHDL', 'SeoulPass', 'SeoulPassV2', 'SiteMinderFake', 'SiteMinder', 'SkiData', 'Skyroam', 'SmartInfini', 'Smartix', 'StarYouBon', 'SunWorld', 'Sunacctg', 'Systex', 'TFC', 'THSROTS', 'THSROTSv2', 'THSRTW', 'Tablenjoy', 'ThreeTGD', 'TicketGo', 'Ticketsuda', 'TourBMS', 'TourCMS', 'TripStore', 'Tripcarte', 'TsimTech', 'TurboJET', 'TwelveCM', 'USIMSA', 'UniveralStudio', 'VPass', 'Varitrip', 'Ventrata', 'VinWonder', 'Vocom', 'WBF', 'WeekN', 'Windbreak', 'Xpark', 'Xplori', 'Yanolja'],
      currentTab: null,
    }
  },
  methods: {
    openUuid() {
      chrome.scripting.executeScript({
        target: {tabId: this.currentTab.id},
        func: () => {
          const kbnDocTableWrapperElement = document.querySelector('.kbnDocTableWrapper');
          if (kbnDocTableWrapperElement) {
            const euiButtonEmptyElement = kbnDocTableWrapperElement.querySelector('.euiButtonEmpty--xSmall');
            if (euiButtonEmptyElement) {
              euiButtonEmptyElement.click();
            }
          }

          setTimeout(() => {
            const spans = document.querySelectorAll('span');
            spans.forEach(span => {
              if (span.textContent.trim() === 'request.uuid') {
                const aElement = span.closest('.euiTableRow').querySelector('a[target="_blank"]');
                if (aElement) {
                  aElement.click();
                }
              }
            });
          }, 500);
        },
        args: []
      }, () => {
      });
    },
    search() {
      console.log(this.form);
      let targetUrl = this.buildSearchUrl(
          new URL(this.currentTab.url).hostname,
          this.buildFormFilters(),
          this.buildDateQuery()
      );

      console.log('targetUrl');
      console.log(targetUrl);
      chrome.tabs.create({url: targetUrl}, function (newTab) {});
    },
    buildFormFilters() {
      let filters = [];
      if (this.form.ifLogLabelResponse) {
        filters.push(this.buildFilter('log_label', 'RESPONSE'));
      }

      if (this.form.ifLogTypeB2BAPI) {
        filters.push(this.buildFilter('log_type', 'kkday-b2b-api'));
      }

      if (this.form.ifMessageB2Border) {
        filters.push(this.buildFilter('message', 'b2bOrder'));
      }

      if (this.form.b2bServiceName && this.form.b2bServiceName !== '---') {
        filters.push(this.buildFilter('custom_kkday-b2b-api.serviceName', this.form.b2bServiceName));
      }

      if (this.form.b2bServiceOrderMid) {
        filters.push(this.buildFilter('custom_kkday-b2b-api.orderMid', this.form.b2bServiceOrderMid));
      }

      return filters;
    },
    buildFilter(key, value) {
      value = "'" + value.trim() + "'";

      return '('+ "'" + '$state' + "'" + ':(store:appState),meta:(alias:!n,disabled:!f,key:' +
          key +
          ',negate:!f,params:(query:' +
          value +
          '),type:phrase),query:(match_phrase:(' + key + ':' + value + ')))';
    },
    buildSearchUrl(domain, filters, dateQuery) {
      return 'https://' + domain +
          '/app/discover?#/?_g=(time:(' + dateQuery + '))&_a=(columns:!(log_type,request.url,custom_kkday-api.trace_url,message,tracing.trace_id),filters:!(' +
          filters.join(',') +
          '))'
    },
    buildDateQuery() {
      switch (this.form.searchDate) {
        case '5min':
          return 'from:now-5m,to:now';
        case '1hour':
          return 'from:now-1h,to:now';
        case '1day':
          return 'from:now-1d,to:now';
        case '1week':
          return 'from:now-1w,to:now';
        case '1month':
          return 'from:now-1M,to:now';
      }

      return 'from:now-5m,to:now';
    }
  },
  created() {
    chrome.tabs.query({currentWindow: true, active: true}, tabs => {
      this.currentTab = tabs[0];
      console.log(this.currentTab.url);
    });
  }
}
</script>

