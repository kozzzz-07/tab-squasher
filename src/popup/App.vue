<template>
  <form @submit.prevent="onDelete">
    <fieldset>
      <legend>タブの削除方法を選択してください。</legend>
      <div>
        <input type="radio" name="select" id="origin" value="origin" v-model="picked" />
        <label for="origin">オリジン一致</label>
        <input type="radio" name="select" id="path" value="path" v-model="picked" />
        <label for="path">パス一致</label>
        <input type="radio" name="select" id="query" value="query" v-model="picked" />
        <label for="query">パラメーター含めて一致</label>
      </div>
      <div>
        <button type="submit">削除</button>
      </div>
    </fieldset>
  </form>
</template>

<script>
export default {
  data() {
    return {
      picked: 'origin',
      queryInfo: { pinned: false },
      deleteTabList: [],
    };
  },
  methods: {
    onDelete: function() {
      if (!confirm('削除してもよろしいですか？')) {
        return;
      }

      chrome.tabs.query(this.queryInfo, tabs => {
        const count = tabs.length;
        this.filterTabs(tabs);
        chrome.tabs.remove(this.deleteTabList, () => {
          const deleteCount = this.deleteTabList.length;
          this.deleteTabList = [];
          alert(`${count}件中、${deleteCount}件削除しました。`);
        });
      });
    },
    getUrl: function(picked, url) {
      const parser = new URL(url);

      switch (picked) {
        case 'origin':
          return parser.origin;

        case 'path':
          return parser.origin + parser.pathname;

        case 'query':
          return parser.href;

        default:
          return '';
      }
    },
    filterTabs: function(tabs) {
      // タブの一覧から重複するもの抽出する（副作用ありまくり）
      const notIncognitoTabs = tabs.filter(tab => !tab.incognito);

      while (notIncognitoTabs.length) {
        const firstTab = notIncognitoTabs.shift();

        const url = this.getUrl(this.picked, firstTab.url);

        for (let i = 0; i < notIncognitoTabs.length; ) {
          if (url === this.getUrl(this.picked, notIncognitoTabs[i].url)) {
            const tab = notIncognitoTabs.splice(i, 1)[0];
            this.deleteTabList.push(tab.id);
            continue;
          }
          i++;
        }
      }
    },
  },
};
</script>

<style lang="scss" scoped>
form {
  min-width: 450px;
}

div:first-of-type {
  display: flex;
  align-items: flex-start;
  margin-bottom: 5px;
}

label {
  margin-right: 15px;
  line-height: 32px;
}

input {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;

  border-radius: 50%;
  width: 16px;
  height: 16px;

  border: 2px solid #999;
  transition: 0.2s all linear;
  margin-right: 5px;

  position: relative;
  top: 4px;
}

input:checked {
  border: 6px solid black;
}

button,
legend {
  color: white;
  background-color: black;
  padding: 5px 10px;
  border-radius: 0;
  border: 0;
  font-size: 14px;
}

button:hover,
button:focus {
  color: #999;
}

button:active {
  background-color: white;
  color: black;
  outline: 1px solid black;
}
</style>
