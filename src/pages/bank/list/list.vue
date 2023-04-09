<template>
  <a-card>
    <!-- <div :class="advanced ? 'search' : null">
      <a-form layout="horizontal">
        <div :class="advanced ? null : 'fold'">
          <a-row>
            <a-col :md="8" :sm="24">
              <a-form-item
                label="规则编号"
                :labelCol="{ span: 5 }"
                :wrapperCol="{ span: 18, offset: 1 }"
              >
                <a-input placeholder="请输入" />
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item
                label="使用状态"
                :labelCol="{ span: 5 }"
                :wrapperCol="{ span: 18, offset: 1 }"
              >
                <a-select placeholder="请选择">
                  <a-select-option value="1">关闭</a-select-option>
                  <a-select-option value="2">运行中</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item
                label="调用次数"
                :labelCol="{ span: 5 }"
                :wrapperCol="{ span: 18, offset: 1 }"
              >
                <a-input-number style="width: 100%" placeholder="请输入" />
              </a-form-item>
            </a-col>
          </a-row>
          <a-row v-if="advanced">
            <a-col :md="8" :sm="24">
              <a-form-item
                label="更新日期"
                :labelCol="{ span: 5 }"
                :wrapperCol="{ span: 18, offset: 1 }"
              >
                <a-date-picker style="width: 100%" placeholder="请输入更新日期" />
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item
                label="使用状态"
                :labelCol="{ span: 5 }"
                :wrapperCol="{ span: 18, offset: 1 }"
              >
                <a-select placeholder="请选择">
                  <a-select-option value="1">关闭</a-select-option>
                  <a-select-option value="2">运行中</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item
                label="描述"
                :labelCol="{ span: 5 }"
                :wrapperCol="{ span: 18, offset: 1 }"
              >
                <a-input placeholder="请输入" />
              </a-form-item>
            </a-col>
          </a-row>
        </div>
        <span style="float: right; margin-top: 3px">
          <a-button type="primary">查询</a-button>
          <a-button style="margin-left: 8px">重置</a-button>
          <a @click="toggleAdvanced" style="margin-left: 8px">
            {{ advanced ? "收起" : "展开" }}
            <a-icon :type="advanced ? 'up' : 'down'" />
          </a>
        </span>
      </a-form>
    </div> -->
    <div>
      <!-- <a-space class="operator">
        <a-button @click="addNew" type="primary">新建</a-button>
        <a-button>批量操作</a-button>
        <a-dropdown>
          <a-menu @click="handleMenuClick" slot="overlay">
            <a-menu-item key="delete">删除</a-menu-item>
            <a-menu-item key="audit">审批</a-menu-item>
          </a-menu>
          <a-button> 更多操作 <a-icon type="down" /> </a-button>
        </a-dropdown>
      </a-space> -->
      <standard-table
        :columns="columns"
        :dataSource="dataSource"
        @clear="onClear"
        @change="onChange"
        :pagination="{ ...pagination, onChange: onPageChange }"
        @selectedRowChange="onSelectChange"
      >
        <template slot="image-column" slot-scope="imageUrl">
          <img :src="imageUrl.text" />
        </template>
        <template slot="status-column" slot-scope="status">
          <span
            v-if="status.text == 'active'" 
            style="
                  background-color: green;
                  color: white;
                  padding: 4px 8px;
                  border-radius: 4px;
                  display: inline-block;
                "
          >
            {{ status.text }}
          </span>
          <span
            v-if="status.text == 'inactive'" 
            style="
                  background-color: red;
                  color: white;
                  padding: 4px 8px;
                  border-radius: 4px;
                  display: inline-block;
                "
          >
            {{ status.text }}
          </span>
          
        </template>

        <div slot="description" slot-scope="{ text }">
          {{ text }}
        </div>
        <div slot="action" slot-scope="{ text, record }">
         
          <a style="margin-right: 8px"> <a-icon type="edit" /> <router-link :to="`/bank/edit/edit/${record.id}`">edit</router-link> </a>
          <a @click="deleteRecord(record.id)"> <a-icon type="delete" />delete </a>
          <!-- <a @click="deleteRecord(record.key)" v-auth="`delete`">
            <a-icon type="delete" />删除2
          </a> -->
         
        </div>
        <template slot="statusTitle">
          <a-icon @click.native="onStatusTitleClick" type="info-circle" />
        </template>
      </standard-table>
    </div>
  </a-card>
</template>

<script>
import StandardTable from "@/components/table/StandardTable";
import { request } from "@/utils/request";
const columns = [
  {
    title: "ID",
    dataIndex: "id",
  },
  {
    title: "Account",
    dataIndex: "accountID",
    scopedSlots: { customRender: "accountID" },
  },
  // {
  //   title: "Name",
  //   dataIndex: "callNo",
  //   sorter: true,
  //   needTotal: true,
  //   customRender: (text) => text + " 次",
  // },
  {
    title: "Image",
    dataIndex: "imageUrl",
    key: "imageUrl",
    scopedSlots: { customRender: "image-column" },
  },
  {
    title: "Name",
    dataIndex: "accountName",
  },

  {
    dataIndex: "Bank",
    needTotal: true,
    slots: { title: "bankName" },
  },
  {
    title: "Status",
    dataIndex: "status",
    key: "status",
    scopedSlots: { customRender: "status-column" },
    sorter: (a, b) => a.status.localeCompare(b.status),
  },
  {
    title: "Action",
    scopedSlots: { customRender: "action" },
  },
];

export default {
  name: "QueryList",
  components: { StandardTable },
  data() {
    return {
      advanced: true,
      columns: columns,
      dataSource: [],
      selectedRows: [],
      pagination: {
        current: 1,
        pageSize: 10,
        total: 0,
      },
    };
  },
  // authorize: {
  //   deleteRecord: "delete",
  // },
  mounted() {
    this.getData();
  },
  methods: {
    onPageChange(page, pageSize) {
      this.pagination.current = page;
      this.pagination.pageSize = pageSize;
      this.getData();
    },
    getData() {
      request(process.env.VUE_APP_API_BASE_URL + "/list", "get", {
        page: this.pagination.current,
        pageSize: this.pagination.pageSize,
      }).then((res) => {
        const { list, page, pageSize, total } = res?.data?.data ?? {};
        console.log(list);
        this.dataSource = [
          {
            id: 1,
            accountID: "ABC123DEF4",
            accountName: "John Doe",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Bank of America",
            status: "active",
          },
          {
            id: 2,
            accountID: "XYZ456GHI7",
            accountName: "Jane Smith",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Wells Fargo",
            status: "inactive",
          },
          {
            id: 3,
            accountID: "LMN789OPQ1",
            accountName: "Robert Johnson",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Chase",
            status: "active",
          },
          {
            id: 4,
            accountID: "RST234UVW5",
            accountName: "Emily Nguyen",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Citibank",
            status: "inactive",
          },
          {
            id: 5,
            accountID: "DEF567GHI8",
            accountName: "David Lee",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Bank of America",
            status: "active",
          },
          {
            id: 6,
            accountID: "JKL901MNO2",
            accountName: "Samantha Kim",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Wells Fargo",
            status: "inactive",
          },
          {
            id: 7,
            accountID: "PQR345STU9",
            accountName: "Michael Johnson",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Chase",
            status: "active",
          },
          {
            id: 8,
            accountID: "VWX678YZA3",
            accountName: "Jennifer Smith",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Citibank",
            status: "inactive",
          },
          {
            id: 9,
            accountID: "BCD123EFG4",
            accountName: "Kevin Lee",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Bank of America",
            status: "active",
          },
          {
            id: 10,
            accountID: "GHI456JKL7",
            accountName: "Jessica Park",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Wells Fargo",
            status: "inactive",
          },
          {
            id: 11,
            accountID: "MNO789PQR1",
            accountName: "William Johnson",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Chase",
            status: "active",
          },
          {
            id: 12,
            accountID: "STU234VWX5",
            accountName: "Linda Smith",
            imageUrl: "https://picsum.photos/200/300",
            bankName: "Citibank",
            status: "inactive",
          },
        ];
        this.pagination.current = page;
        this.pagination.pageSize = pageSize;
        this.pagination.total = total;
      });
    },
    deleteRecord(id) {
      this.dataSource = this.dataSource.filter((item) => item.id !== id);
      this.selectedRows = this.selectedRows.filter((item) => item.id !== id);
    },
    toggleAdvanced() {
      this.advanced = !this.advanced;
    },
    remove() {
      this.dataSource = this.dataSource.filter(
        (item) => this.selectedRows.findIndex((row) => row.key === item.key) === -1
      );
      this.selectedRows = [];
    },
    onClear() {
      this.$message.info("您清空了勾选的所有行");
    },
    onStatusTitleClick() {
      this.$message.info("你点击了状态栏表头");
    },
    onChange() {
      this.$message.info("表格状态改变了");
    },
    onSelectChange() {
      this.$message.info("选中行改变了");
    },
    addNew() {
      this.dataSource.unshift({
        key: this.dataSource.length,
        no: "NO " + this.dataSource.length,
        description: "这是一段描述",
        callNo: Math.floor(Math.random() * 1000),
        status: Math.floor(Math.random() * 10) % 4,
        updatedAt: "2018-07-26",
      });
    },
    handleMenuClick(e) {
      if (e.key === "delete") {
        this.remove();
      }
    },
  },
};
</script>

<style lang="less" scoped>
.search {
  margin-bottom: 54px;
}
.fold {
  width: calc(100% - 216px);
  display: inline-block;
}
.operator {
  margin-bottom: 18px;
}
@media screen and (max-width: 900px) {
  .fold {
    width: 100%;
  }
}
</style>
