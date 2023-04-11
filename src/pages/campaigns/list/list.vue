
<template>
    <div>
        <a-card>
            <a-row>

            </a-row>
        </a-card>
        <a-card>
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

                        :pagination="{ ...pagination, onChange: onPageChange }"

                >
                    <template slot="image-column" slot-scope="image">
                        <img :src="image.text"/>
                    </template>
                    <template slot="status-column" slot-scope="status">
          <span
                  v-if="status.text != 1"
                  style="
              background-color: green;
              color: white;
              padding: 4px 8px;
              border-radius: 4px;
              display: inline-block;
            "
          >
            active
          </span>
                        <span
                                v-if="status.text == 1"
                                style="
              background-color: red;
              color: white;
              padding: 4px 8px;
              border-radius: 4px;
              display: inline-block;
            "
                        >
            inactive
          </span>
                    </template>

                    <div slot="description" slot-scope="{ text }">
                        {{ text }}
                    </div>

                    <div slot = "date-public-column" slot-scope="date_public">

                        <div>
                            <p>{{formatTime(date_public.text)}}</p>
                        </div>
                    </div>
                    <div slot = "date-end-column" slot-scope="date_end">

                        <div>
                            <p>{{formatTime(date_end.text)}}</p>
                        </div>
                    </div>
                    <div slot="action" slot-scope="{  record }">
                        <a style="margin-right: 8px">
                            <a-icon type="edit"/>
                            <router-link :to="`/language/edit/edit/${record.id}`"
                            >edit
                            </router-link
                            >
                        </a>
                        <a @click="deleteRecord(record.id)">
                            <a-icon type="delete"/>
                            delete
                        </a>
                        <!-- <a @click="deleteRecord(record.key)" v-auth="`delete`">
                                    <a-icon type="delete" />删除2
                                  </a> -->
                    </div>
                    <template slot="statusTitle">
                        <a-icon @click.native="onStatusTitleClick" type="info-circle"/>
                    </template>
                </standard-table>
            </div>
        </a-card>
    </div>

</template>

<script>


import StandardTable from "@/components/table/StandardTable";
import {request} from "@/utils/request";
import moment from "moment";

const columns = [
    {
        title: "ID",
        dataIndex: "id",
    },
    {
        title: "Name",
        dataIndex: "name",
    },
    {
        title: "Image",
        dataIndex: "image",
        key: "image",
        scopedSlots: {customRender: "image-column"},
    },
    {
        title: "Public Date",
        dataIndex: "date_public",
        key: "date_public",
        scopedSlots: {customRender: "date-public-column"},

        // render: (record) => {
        //     console.log(record);
        //     return (
        //         <div>
        //             <p>{moment(record.date_public).format("DD-MM-YYYY")}</p>
        //         </div>
        //     );
        // },
    },
    {
        title: "End Date",
        dataIndex: "date_end",
        key: "date_end",
        scopedSlots: {customRender: "date-end-column"},

        // render: (record) => {
        //     return (
        //         <div>
        //             <p>{moment(record.date_public).format("DD-MM-YYYY")}</p>
        //         </div>
        //     );
        // },
    },
    {
        title: "Status",
        dataIndex: "status",
        key: "status",
        scopedSlots: {customRender: "status-column"},
        sorter: (a, b) => a.status.localeCompare(b.status),
    },
    {
        title: "Action",
        scopedSlots: {customRender: "action"},
    },
];

export default {
    name: "QueryList",
    components: {StandardTable},
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
        formatTime(time){
            const result = moment(time).format("DD-MM-YYYY");
            if (result === "Invalid date") {
                return "no date";
            }
            return result
        },
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
                const { page, pageSize, total} = res?.data?.data ?? {};

                
                this.dataSource = [
                    {
                        "id": 96,
                        "name": "McCann Asia",
                        "description": "McCann Asia là một nền tảng trung gian kết nối các công ty thương mại và dịch vụ trực tuyến về thương mại điện tử, bán lẻ, ngân hàng và tài chính…và đặt chỗ trực tuyến vơi các đối tác phương tiện truyền thông để quảng bá sản phẩm đến người dùng.\nMcCann Asia là nền tảng quảng cáo lớn và uy tín nhất Châu Á với hơn 2.000.000 thành viên và hàng trăm chiến dịch tham gia.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "McCann Asia là một nền tảng trung gian kết nối các công ty thương mại và dịch vụ trực tuyến về thương mại điện tử, bán lẻ, ngân hàng và tài chính…và đặt chỗ trực tuyến vơi các đối tác phương tiện truyền thông để quảng bá sản phẩm đến người dùng.\nMcCann Asia là nền tảng quảng cáo lớn và uy tín nhất Châu Á với hơn 2.000.000 thành viên và hàng trăm chiến dịch tham gia.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 2 l\u1ea7n tr\u00ean ng\u00e0y v\u00e0 gi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 2 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft ng\u00e2n h\u00e0ng th\u00e0nh c\u00f4ng tr\u00ean trang Mccannasia.com\"]",
                        "date_public": "2023-03-03 14:54:30",
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 2000000,
                        "code": null,
                        "price": 70000,
                        "status": null,
                        "users": "[3,1]",
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-10 11:13:36",
                        "category": 6,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 97,
                        "name": "VNPAY",
                        "description": 'VNPAY - Ví điện tử đầu tiên dành cho gia đình Việt, đáp ứng đa dạng nhu cầu thanh toán các dịch vụ hàng ngày của bạn và gia đình một cách nhanh chóng, tiện lợi, an toàn.\nQuản lý tiện lợi với Ví gia đình, mở ví thành viên cho người thân, đặt hạn mức và theo dõi chi tiêu dễ dàng. Mua sắm cùng VNPAYQR, quét mã VNPAYQR thanh toán siêu nhanh, nhận ưu đãi độc quyền tại hơn 150.000 điểm thanh toán cùng nhiều tính năng khác như chuyển tiền, Nạp tiền điện thoại, Thanh toán hóa đơn, Đặt vé máy bay, Đặt vé tàu, Gọi taxi…\n\nTrở thành đối tác quảng cáo chuyên nghiệp chưa bao giờ dễ dàng đến thế thông qua McCann Asia.\nĐăng ký ngay tại : https://mccannasia.com',
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "VNPAY - Ví điện tử đầu tiên dành cho gia đình Việt, đáp ứng đa dạng nhu cầu thanh toán các dịch vụ hàng ngày của bạn và gia đình một cách nhanh chóng, tiện lợi, an toàn.\nQuản lý tiện lợi với Ví gia đình, mở ví thành viên cho người thân, đặt hạn mức và theo dõi chi tiêu dễ dàng. Mua sắm cùng VNPAYQR, quét mã VNPAYQR thanh toán siêu nhanh, nhận ưu đãi độc quyền tại hơn 150.000 điểm thanh toán cùng nhiều tính năng khác như chuyển tiền, Nạp tiền điện thoại, Thanh toán hóa đơn, Đặt vé máy bay, Đặt vé tàu, Gọi taxi…\n\nTrở thành đối tác quảng cáo chuyên nghiệp chưa bao giờ dễ dàng đến thế thông qua McCann Asia.\nĐăng ký ngay tại : https://mccannasia.com/",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 2 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nTrong 1 th\u00e1ng c\u1ea7n gi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 5 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 2000000,
                        "code": null,
                        "price": 70000,
                        "status": null,
                        "users": "[3]",
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-10 12:16:55",
                        "category": 1,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 98,
                        "name": "{\"en\":\"Bank S\u1ed1 \u0110\u1eb9p\",\"vi\":\"Bank S\u1ed1 \u0110\u1eb9p\",\"cn\":\"Bank S\u1ed1 \u0110\u1eb9p\"}",
                        "description": "{\"en\":\"<p>Bank S\u1ed1 \u0110\u1eb9p l&agrave; d\u1ecbch v\u1ee5 m\u1edf t&agrave;i kho\u1ea3n c\u1ee7a c&aacute;c ng&acirc;n h&agrave;ng\\/v&iacute; \u0111i\u1ec7n t\u1eed ch\u1ec9 trong v&ograve;ng 1 click. D\u1ecbch v\u1ee5 Bank S\u1ed1 \u0110\u1eb9p v\u1edbi \u01b0u \u0111i\u1ec3m nhanh g\u1ecdn, ti\u1ec7n l\u1ee3i, online 100% kh&ocirc;ng c\u1ea7n ph\u1ea3i ra ng&acirc;n h&agrave;ng. Bank S\u1ed1 \u0110\u1eb9p l&agrave; m\u1ed9t website t\u1ed5ng h\u1ee3p nhi\u1ec1u ng&acirc;n h&agrave;ng\\/v&iacute; \u0111i\u1ec7n t\u1eed kh&aacute;c nhau gi&uacute;p cho kh&aacute;ch h&agrave;ng kh&ocirc;ng ph\u1ea3i m\u1ea5t th\u1eddi gian t&igrave;m hi\u1ec3u v&agrave; l\u1ef1a ch\u1ecdn. Kh&aacute;ch h&agrave;ng l\u1ef1a ch\u1ecdn m\u1edf t&agrave;i kho\u1ea3n tr&ecirc;n website Bank S\u1ed1 \u0110\u1eb9p, s\u1ebd nh\u1eadn ngay voucher mua s\u1eafm tr&ecirc;n c&aacute;c s&agrave;n th\u01b0\u01a1ng m\u1ea1i \u0111i\u1ec7n t\u1eed. Tr\u1edf tha\u0300nh \u0111&ocirc;\u0301i ta\u0301c qua\u0309ng ca\u0301o chuy&ecirc;n nghi&ecirc;\u0323p ch\u01b0a bao gi\u1edd d&ecirc;\u0303 da\u0300ng \u0111&ecirc;\u0301n th&ecirc;\u0301 th&ocirc;ng qua McCann Asia. \u0110\u0103ng ky\u0301 ngay ta\u0323i : https:\\/\\/mccannasia.com\\/<\\/p>\",\"vi\":\"<p>Bank S\u1ed1 \u0110\u1eb9p l&agrave; d\u1ecbch v\u1ee5 m\u1edf t&agrave;i kho\u1ea3n c\u1ee7a c&aacute;c ng&acirc;n h&agrave;ng\\/v&iacute; \u0111i\u1ec7n t\u1eed ch\u1ec9 trong v&ograve;ng 1 click. D\u1ecbch v\u1ee5 Bank S\u1ed1 \u0110\u1eb9p v\u1edbi \u01b0u \u0111i\u1ec3m nhanh g\u1ecdn, ti\u1ec7n l\u1ee3i, online 100% kh&ocirc;ng c\u1ea7n ph\u1ea3i ra ng&acirc;n h&agrave;ng. Bank S\u1ed1 \u0110\u1eb9p l&agrave; m\u1ed9t website t\u1ed5ng h\u1ee3p nhi\u1ec1u ng&acirc;n h&agrave;ng\\/v&iacute; \u0111i\u1ec7n t\u1eed kh&aacute;c nhau gi&uacute;p cho kh&aacute;ch h&agrave;ng kh&ocirc;ng ph\u1ea3i m\u1ea5t th\u1eddi gian t&igrave;m hi\u1ec3u v&agrave; l\u1ef1a ch\u1ecdn. Kh&aacute;ch h&agrave;ng l\u1ef1a ch\u1ecdn m\u1edf t&agrave;i kho\u1ea3n tr&ecirc;n website Bank S\u1ed1 \u0110\u1eb9p, s\u1ebd nh\u1eadn ngay voucher mua s\u1eafm tr&ecirc;n c&aacute;c s&agrave;n th\u01b0\u01a1ng m\u1ea1i \u0111i\u1ec7n t\u1eed. Tr\u1edf tha\u0300nh \u0111&ocirc;\u0301i ta\u0301c qua\u0309ng ca\u0301o chuy&ecirc;n nghi&ecirc;\u0323p ch\u01b0a bao gi\u1edd d&ecirc;\u0303 da\u0300ng \u0111&ecirc;\u0301n th&ecirc;\u0301 th&ocirc;ng qua McCann Asia. \u0110\u0103ng ky\u0301 ngay ta\u0323i : https:\\/\\/mccannasia.com\\/<\\/p>\",\"cn\":\"<p>Bank S\u1ed1 \u0110\u1eb9p l&agrave; d\u1ecbch v\u1ee5 m\u1edf t&agrave;i kho\u1ea3n c\u1ee7a c&aacute;c ng&acirc;n h&agrave;ng\\/v&iacute; \u0111i\u1ec7n t\u1eed ch\u1ec9 trong v&ograve;ng 1 click. D\u1ecbch v\u1ee5 Bank S\u1ed1 \u0110\u1eb9p v\u1edbi \u01b0u \u0111i\u1ec3m nhanh g\u1ecdn, ti\u1ec7n l\u1ee3i, online 100% kh&ocirc;ng c\u1ea7n ph\u1ea3i ra ng&acirc;n h&agrave;ng. Bank S\u1ed1 \u0110\u1eb9p l&agrave; m\u1ed9t website t\u1ed5ng h\u1ee3p nhi\u1ec1u ng&acirc;n h&agrave;ng\\/v&iacute; \u0111i\u1ec7n t\u1eed kh&aacute;c nhau gi&uacute;p cho kh&aacute;ch h&agrave;ng kh&ocirc;ng ph\u1ea3i m\u1ea5t th\u1eddi gian t&igrave;m hi\u1ec3u v&agrave; l\u1ef1a ch\u1ecdn. Kh&aacute;ch h&agrave;ng l\u1ef1a ch\u1ecdn m\u1edf t&agrave;i kho\u1ea3n tr&ecirc;n website Bank S\u1ed1 \u0110\u1eb9p, s\u1ebd nh\u1eadn ngay voucher mua s\u1eafm tr&ecirc;n c&aacute;c s&agrave;n th\u01b0\u01a1ng m\u1ea1i \u0111i\u1ec7n t\u1eed. Tr\u1edf tha\u0300nh \u0111&ocirc;\u0301i ta\u0301c qua\u0309ng ca\u0301o chuy&ecirc;n nghi&ecirc;\u0323p ch\u01b0a bao gi\u1edd d&ecirc;\u0303 da\u0300ng \u0111&ecirc;\u0301n th&ecirc;\u0301 th&ocirc;ng qua McCann Asia. \u0110\u0103ng ky\u0301 ngay ta\u0323i : https:\\/\\/mccannasia.com\\/<\\/p>\"}",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "{\"en\":\"Bank S\u1ed1 \u0110\u1eb9p l\u00e0 d\u1ecbch v\u1ee5 m\u1edf t\u00e0i kho\u1ea3n c\u1ee7a c\u00e1c ng\u00e2n h\u00e0ng\\/v\u00ed \u0111i\u1ec7n t\u1eed ch\u1ec9 trong v\u00f2ng 1 click. D\u1ecbch v\u1ee5 Bank S\u1ed1 \u0110\u1eb9p v\u1edbi \u01b0u \u0111i\u1ec3m nhanh g\u1ecdn, ti\u1ec7n l\u1ee3i, online 100% kh\u00f4ng c\u1ea7n ph\u1ea3i ra ng\u00e2n h\u00e0ng.Bank S\u1ed1 \u0110\u1eb9p l\u00e0 m\u1ed9t website t\u1ed5ng h\u1ee3p nhi\u1ec1u ng\u00e2n h\u00e0ng\\/v\u00ed \u0111i\u1ec7n t\u1eed kh\u00e1c nhau gi\u00fap cho kh\u00e1ch h\u00e0ng kh\u00f4ng ph\u1ea3i m\u1ea5t th\u1eddi gian t\u00ecm hi\u1ec3u v\u00e0 l\u1ef1a ch\u1ecdn.Kh\u00e1ch h\u00e0ng l\u1ef1a ch\u1ecdn m\u1edf t\u00e0i kho\u1ea3n tr\u00ean website Bank S\u1ed1 \u0110\u1eb9p, s\u1ebd nh\u1eadn ngay voucher mua s\u1eafm tr\u00ean c\u00e1c s\u00e0n th\u01b0\u01a1ng m\u1ea1i \u0111i\u1ec7n t\u1eed.Tr\u1edf tha\u0300nh \u0111\u00f4\u0301i ta\u0301c qua\u0309ng ca\u0301o chuy\u00ean nghi\u00ea\u0323p ch\u01b0a bao gi\u1edd d\u00ea\u0303 da\u0300ng \u0111\u00ea\u0301n th\u00ea\u0301 th\u00f4ng qua McCann Asia.\u0110\u0103ng ky\u0301 ngay ta\u0323i : https:\\/\\/mccannasia.com\\/\",\"vi\":\"Bank S\u1ed1 \u0110\u1eb9p l\u00e0 d\u1ecbch v\u1ee5 m\u1edf t\u00e0i kho\u1ea3n c\u1ee7a c\u00e1c ng\u00e2n h\u00e0ng\\/v\u00ed \u0111i\u1ec7n t\u1eed ch\u1ec9 trong v\u00f2ng 1 click. D\u1ecbch v\u1ee5 Bank S\u1ed1 \u0110\u1eb9p v\u1edbi \u01b0u \u0111i\u1ec3m nhanh g\u1ecdn, ti\u1ec7n l\u1ee3i, online 100% kh\u00f4ng c\u1ea7n ph\u1ea3i ra ng\u00e2n h\u00e0ng.Bank S\u1ed1 \u0110\u1eb9p l\u00e0 m\u1ed9t website t\u1ed5ng h\u1ee3p nhi\u1ec1u ng\u00e2n h\u00e0ng\\/v\u00ed \u0111i\u1ec7n t\u1eed kh\u00e1c nhau gi\u00fap cho kh\u00e1ch h\u00e0ng kh\u00f4ng ph\u1ea3i m\u1ea5t th\u1eddi gian t\u00ecm hi\u1ec3u v\u00e0 l\u1ef1a ch\u1ecdn.Kh\u00e1ch h\u00e0ng l\u1ef1a ch\u1ecdn m\u1edf t\u00e0i kho\u1ea3n tr\u00ean website Bank S\u1ed1 \u0110\u1eb9p, s\u1ebd nh\u1eadn ngay voucher mua s\u1eafm tr\u00ean c\u00e1c s\u00e0n th\u01b0\u01a1ng m\u1ea1i \u0111i\u1ec7n t\u1eed.Tr\u1edf tha\u0300nh \u0111\u00f4\u0301i ta\u0301c qua\u0309ng ca\u0301o chuy\u00ean nghi\u00ea\u0323p ch\u01b0a bao gi\u1edd d\u00ea\u0303 da\u0300ng \u0111\u00ea\u0301n th\u00ea\u0301 th\u00f4ng qua McCann Asia.\u0110\u0103ng ky\u0301 ngay ta\u0323i : https:\\/\\/mccannasia.com\\/\",\"cn\":\"Bank S\u1ed1 \u0110\u1eb9p l\u00e0 d\u1ecbch v\u1ee5 m\u1edf t\u00e0i kho\u1ea3n c\u1ee7a c\u00e1c ng\u00e2n h\u00e0ng\\/v\u00ed \u0111i\u1ec7n t\u1eed ch\u1ec9 trong v\u00f2ng 1 click. D\u1ecbch v\u1ee5 Bank S\u1ed1 \u0110\u1eb9p v\u1edbi \u01b0u \u0111i\u1ec3m nhanh g\u1ecdn, ti\u1ec7n l\u1ee3i, online 100% kh\u00f4ng c\u1ea7n ph\u1ea3i ra ng\u00e2n h\u00e0ng.Bank S\u1ed1 \u0110\u1eb9p l\u00e0 m\u1ed9t website t\u1ed5ng h\u1ee3p nhi\u1ec1u ng\u00e2n h\u00e0ng\\/v\u00ed \u0111i\u1ec7n t\u1eed kh\u00e1c nhau gi\u00fap cho kh\u00e1ch h\u00e0ng kh\u00f4ng ph\u1ea3i m\u1ea5t th\u1eddi gian t\u00ecm hi\u1ec3u v\u00e0 l\u1ef1a ch\u1ecdn.Kh\u00e1ch h\u00e0ng l\u1ef1a ch\u1ecdn m\u1edf t\u00e0i kho\u1ea3n tr\u00ean website Bank S\u1ed1 \u0110\u1eb9p, s\u1ebd nh\u1eadn ngay voucher mua s\u1eafm tr\u00ean c\u00e1c s\u00e0n th\u01b0\u01a1ng m\u1ea1i \u0111i\u1ec7n t\u1eed.Tr\u1edf tha\u0300nh \u0111\u00f4\u0301i ta\u0301c qua\u0309ng ca\u0301o chuy\u00ean nghi\u00ea\u0323p ch\u01b0a bao gi\u1edd d\u00ea\u0303 da\u0300ng \u0111\u00ea\u0301n th\u00ea\u0301 th\u00f4ng qua McCann Asia.\u0110\u0103ng ky\u0301 ngay ta\u0323i : https:\\/\\/mccannasia.com\\/\"}",
                        "link_": null,
                        "image": "{\"en\":\"\\/upload\\/images\\/image_2023-03-03_14-47-14.png\",\"vi\":null,\"cn\":null}",
                        "list_task": "{\"en\":[null],\"vi\":[null],\"cn\":[null]}",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 5000000,
                        "code": null,
                        "price": 168000,
                        "status": null,
                        "users": "[3]",
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-08 16:43:27",
                        "category": 1,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 99,
                        "name": "BTASKEE - Ứng dụng giúp việc nhà số 1",
                        "description": "Công ty TNHH BTaskee được thành lập vào ngày 30 tháng 03 năm 2016 bởi CEO – Founder Nathan Do (Đỗ Đắc Nhân Tâm).\nBTaskee là doanh nghiệp tiên phong trong việc ứng dụng công nghệ vào ngành giúp việc nhà ở Việt Nam. Chúng tôi cung cấp đa dịch vụ tiện ích như: dọn dẹp nhà, vệ sinh máy lạnh, đi chợ, … tại Đông Nam Á. Thông qua ứng dụng đặt lịch dành cho khách hàng bTaskee và ứng dụng nhận việc của cộng tác viên bTaskee Partner, khách hàng và cộng tác viên có thể chủ động đăng và nhận việc trực tiếp trên ứng dụng.\n\nTrở thành đối tác quảng cáo chuyên nghiệp chưa bao giờ dễ dàng đến thế thông qua McCann Asia.\nĐăng ký ngay tại : https://mccannasia.com/",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Công ty TNHH BTaskee được thành lập vào ngày 30 tháng 03 năm 2016 bởi CEO – Founder Nathan Do (Đỗ Đắc Nhân Tâm).\nBTaskee là doanh nghiệp tiên phong trong việc ứng dụng công nghệ vào ngành giúp việc nhà ở Việt Nam. Chúng tôi cung cấp đa dịch vụ tiện ích như: dọn dẹp nhà, vệ sinh máy lạnh, đi chợ, … tại Đông Nam Á. Thông qua ứng dụng đặt lịch dành cho khách hàng bTaskee và ứng dụng nhận việc của cộng tác viên bTaskee Partner, khách hàng và cộng tác viên có thể chủ động đăng và nhận việc trực tiếp trên ứng dụng.\n\nTrở thành đối tác quảng cáo chuyên nghiệp chưa bao giờ dễ dàng đến thế thông qua McCann Asia.\nĐăng ký ngay tại : https://mccannasia.com/",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nTrong 1 th\u00e1ng c\u1ea7n gi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 6 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 1 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 5000000,
                        "code": null,
                        "price": 168000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 1,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 100,
                        "name": "MB Bank - MỞ TÀI KHOẢN",
                        "description": "Ngân hàng Quân đội - viết tắt là MB, là một ngân hàng thương mại cổ phần của Việt Nam, một doanh nghiệp trực thuộc Bộ Quốc phòng. Ngày 14/02/2020 MB ra mắt phiên bản App mới đi kèm vô vàn khuyến mãi dành cho khách hàng MB cũng như các khách hàng mới. ACCESSTRADE sẽ đồng hành cùng MB Bank trong chiến dịch ra mắt phiên bản App mới.\n\nTrở thành đối tác quảng cáo chuyên nghiệp chưa bao giờ dễ dàng đến thế thông qua McCann Asia.\nĐăng ký ngay tại : https://mccannasia.com/",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Ngân hàng Quân đội - viết tắt là MB, là một ngân hàng thương mại cổ phần của Việt Nam, một doanh nghiệp trực thuộc Bộ Quốc phòng. Ngày 14/02/2020 MB ra mắt phiên bản App mới đi kèm vô vàn khuyến mãi dành cho khách hàng MB cũng như các khách hàng mới. ACCESSTRADE sẽ đồng hành cùng MB Bank trong chiến dịch ra mắt phiên bản App mới.\n\nTrở thành đối tác quảng cáo chuyên nghiệp chưa bao giờ dễ dàng đến thế thông qua McCann Asia.\nĐăng ký ngay tại : https://mccannasia.com/",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 2 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nTrong 1 th\u00e1ng c\u1ea7n gi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 5 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 2000000,
                        "code": null,
                        "price": 70000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 1,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 101,
                        "name": "VP Bank - Mở Tài Khoản Ngân Hàng (Web)",
                        "description": "Ngân hàng TMCP Việt Nam Thịnh Vượng (viết tắt VPBank) là một ngân hàng ở Việt Nam được thành lập ngày 12 tháng 08 năm 1993. Sau 21 năm hoạt động, VPBank đã nâng vốn điều lệ lên 6.347 tỷ đồng, phát triển mạng lưới lên hơn 200 điểm giao dịch, với đội ngũ trên 7.000 cán bộ nhân viên. Là thành viên của nhóm các ngân hàng lớn hàng đầu Việt Nam (G12). \nVPBANk eKYC là giải pháp định danh khách hàng trực tuyến, cho phép ngân hàng vượt qua mọi rào cản địa lý và thời gian để định danh khách hàng 100% online dựa vào các thông tin sinh trắc học (biometrics) mà không cần gặp mặt trực tiếp như quy trình hiện tại.\n\nTrở thành đối tác quảng cáo chuyên nghiệp chưa bao giờ dễ dàng đến thế thông qua McCann Asia.\nĐăng ký ngay tại : https://mccannasia.com/",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Ngân hàng TMCP Việt Nam Thịnh Vượng (viết tắt VPBank) là một ngân hàng ở Việt Nam được thành lập ngày 12 tháng 08 năm 1993. Sau 21 năm hoạt động, VPBank đã nâng vốn điều lệ lên 6.347 tỷ đồng, phát triển mạng lưới lên hơn 200 điểm giao dịch, với đội ngũ trên 7.000 cán bộ nhân viên. Là thành viên của nhóm các ngân hàng lớn hàng đầu Việt Nam (G12). \nVPBANk eKYC là giải pháp định danh khách hàng trực tuyến, cho phép ngân hàng vượt qua mọi rào cản địa lý và thời gian để định danh khách hàng 100% online dựa vào các thông tin sinh trắc học (biometrics) mà không cần gặp mặt trực tiếp như quy trình hiện tại.\n\nTrở thành đối tác quảng cáo chuyên nghiệp chưa bao giờ dễ dàng đến thế thông qua McCann Asia.\nĐăng ký ngay tại : https://mccannasia.com/",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nTrong 1 th\u00e1ng c\u1ea7n gi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 6 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 1 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 5000000,
                        "code": null,
                        "price": 168000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 1,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 102,
                        "name": "Doctor Đồng",
                        "description": "Doctor Đồng là dịch vụ tư vấn tài chính cho các khoản vay cầm cố ngắn hạn với khoản vay từ 1 đến 10 triệu đồng, các lựa chọn kỳ hạn vay là 10, 20 hoặc 30 ngày được cung cấp bởi các đối tác cho vay của chúng tôi. Hiện nay, đối tác cho vay chiến lược các khoản vay cầm cố của Doctor Đồng là Công ty TNHH MTV TMDV Vạn An Phát (VAP).Thanh toán khoản vay sẽ được thực hiện vào cuối kỳ hạn như quy định trong Hợp đồng Vay cầm cố. Thông tin chi tiết bạn hãy xem tại: https://doctordong.vn/faq",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Doctor Đồng là dịch vụ tư vấn tài chính cho các khoản vay cầm cố ngắn hạn với khoản vay từ 1 đến 10 triệu đồng, các lựa chọn kỳ hạn vay là 10, 20 hoặc 30 ngày được cung cấp bởi các đối tác cho vay của chúng tôi. Hiện nay, đối tác cho vay chiến lược các khoản vay cầm cố của Doctor Đồng là Công ty TNHH MTV TMDV Vạn An Phát (VAP).Thanh toán khoản vay sẽ được thực hiện vào cuối kỳ hạn như quy định trong Hợp đồng Vay cầm cố. Thông tin chi tiết bạn hãy xem tại: https://doctordong.vn/faq",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 8 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 3 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 10000000,
                        "code": null,
                        "price": 330000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 103,
                        "name": "Robocash - Hoa hồng cao nhất thị trường",
                        "description": "Robocash cung cấp dịch vụ cho vay nhanh online hoàn toàn tự động, giải ngân trong ngày. Bất kỳ ngày nào trong tuần, bất kể thời gian nào, ngày lễ hoặc ngày nghỉ cũng được cho duyệt vay. \"Robocash\" là hệ thống hiện đại tân tiến, có thể tự động xử lý 1800 dữ liệu khách hàng cùng một lúc dựa vào thông tin được cung cấp theo mẫu.\n\nTrở thành đối tác quảng cáo chuyên nghiệp chưa bao giờ dễ dàng đến thế thông qua McCann Asia.\nĐăng ký ngay tại : https://mccannasia.com/",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Robocash cung cấp dịch vụ cho vay nhanh online hoàn toàn tự động, giải ngân trong ngày. Bất kỳ ngày nào trong tuần, bất kể thời gian nào, ngày lễ hoặc ngày nghỉ cũng được cho duyệt vay. \"Robocash\" là hệ thống hiện đại tân tiến, có thể tự động xử lý 1800 dữ liệu khách hàng cùng một lúc dựa vào thông tin được cung cấp theo mẫu.\n\nTrở thành đối tác quảng cáo chuyên nghiệp chưa bao giờ dễ dàng đến thế thông qua McCann Asia.\nĐăng ký ngay tại : https://mccannasia.com/",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 10 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 4 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 15000000,
                        "code": null,
                        "price": 500000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 104,
                        "name": "Senmo - Vay Tiền Nhanh",
                        "description": "Senmo là một công ty tư vấn tài chính lớn chuyên cho vay trực tuyến. Với quy trình cho vay hoàn toàn trực tuyến, quy trình vay đơn giản dễ hiểu, điều kiện thuận lợi.\n\nLợi ích chương trình đối tác:\n\nĐiều kiện cho vay thuận lợi;\nLãi suất thấp;\nTối thiểu nhất điều kiện bắt buộc để vay vốn.\nLợi thế và tính năng cạnh tranh:\n\nQuy trình đơn giản để gia hạn khoản vay;\nKhả năng kéo dài thời gian hoàn trả nợ; \nThời gian xác nhận khoảng vay ngắn trong vòng 6 phút;\nSố tiền vay lên đến 20 Triệu VND;\nLãi suất hấp dẫn 1,85%.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Senmo là một công ty tư vấn tài chính lớn chuyên cho vay trực tuyến. Với quy trình cho vay hoàn toàn trực tuyến, quy trình vay đơn giản dễ hiểu, điều kiện thuận lợi.\n\nLợi ích chương trình đối tác:\n\nĐiều kiện cho vay thuận lợi;\nLãi suất thấp;\nTối thiểu nhất điều kiện bắt buộc để vay vốn.\nLợi thế và tính năng cạnh tranh:\n\nQuy trình đơn giản để gia hạn khoản vay;\nKhả năng kéo dài thời gian hoàn trả nợ; \nThời gian xác nhận khoảng vay ngắn trong vòng 6 phút;\nSố tiền vay lên đến 20 Triệu VND;\nLãi suất hấp dẫn 1,85%.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nTrong 1 th\u00e1ng c\u1ea7n gi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 6 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 1 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 5000000,
                        "code": null,
                        "price": 168000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 105,
                        "name": "Tamo",
                        "description": "Tamo là nền tảng tư vấn và cung cấp các giải pháp tài chính trực tuyến 24/7, nhằm hỗ trợ cho các nhu cầu tài chính đột xuất của bạn. Thấu hiểu được những vấn đề tài chính bạn đang gặp phải, chúng tôi cố gắng mang đến bạn những giải pháp tài chính đơn giản, nhanh chóng và thuận tiện nhất.\n\nƯu điểm khi khách hàng vay qua Tamo:\n- Nhận xét duyệt hồ sơ chỉ trong 3 phút\n- Không yêu cầu chứng từ bảo lãnh hoặc chứng minh thu nhập\n- Đăng ký vay 24/7 nhanh chóng, thuận tiện và dễ dàng\n- Tỷ lệ duyệt đơn cao",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Tamo là nền tảng tư vấn và cung cấp các giải pháp tài chính trực tuyến 24/7, nhằm hỗ trợ cho các nhu cầu tài chính đột xuất của bạn. Thấu hiểu được những vấn đề tài chính bạn đang gặp phải, chúng tôi cố gắng mang đến bạn những giải pháp tài chính đơn giản, nhanh chóng và thuận tiện nhất.\n\nƯu điểm khi khách hàng vay qua Tamo:\n- Nhận xét duyệt hồ sơ chỉ trong 3 phút\n- Không yêu cầu chứng từ bảo lãnh hoặc chứng minh thu nhập\n- Đăng ký vay 24/7 nhanh chóng, thuận tiện và dễ dàng\n- Tỷ lệ duyệt đơn cao",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 10 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 4 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 15000000,
                        "code": null,
                        "price": 500000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 106,
                        "name": "MONEYCAT - VAY TIỀN 0% LÃI",
                        "description": "MoneyCat là một trong những công ty tư vấn tài chính lớn uy tín nhất hiện nay. \nDịch vụ MoneyCat cung cấp giải pháp tài chính cho khách hàng chỉ trong vòng 5 phút điền thông tin và cam kết giải ngân trong ngày.\n\nƯu đãi đặc biệt dành cho khách hàng mới: 0% lãi suất cho 7 ngày đầu tiên kể từ khi giải ngân.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "MoneyCat là một trong những công ty tư vấn tài chính lớn uy tín nhất hiện nay. \nDịch vụ MoneyCat cung cấp giải pháp tài chính cho khách hàng chỉ trong vòng 5 phút điền thông tin và cam kết giải ngân trong ngày.\n\nƯu đãi đặc biệt dành cho khách hàng mới: 0% lãi suất cho 7 ngày đầu tiên kể từ khi giải ngân.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 10 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 4 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 15000000,
                        "code": null,
                        "price": 500000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 107,
                        "name": "ONCREDIT CPS - 0% lãi Gói vay đầu",
                        "description": "OnCredit tự hào là công ty hàng đầu Việt Nam trong lĩnh vực cho vay tiền online, đem đến giải pháp tài chính Tối Ưu, An Toàn và Minh Bạch cho mọi khách hàng. \n\nOnCredit cho vay tiền online mọi lúc mọi nơi 24/7 kể cả thứ 7, chủ nhật và ngày lễ, giúp mọi người tiết kiệm thời gian, dễ dàng tiếp cận dịch vụ tài chính văn minh, hiện đại; nhận tiền về tài khoản nhanh chóng. Lãi suất vay theo ngày, không theo chu kỳ; khách hàng có thể tự do lựa chọn khoản vay với chính sách ưu đãi hấp dẫn.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "OnCredit tự hào là công ty hàng đầu Việt Nam trong lĩnh vực cho vay tiền online, đem đến giải pháp tài chính Tối Ưu, An Toàn và Minh Bạch cho mọi khách hàng. \n\nOnCredit cho vay tiền online mọi lúc mọi nơi 24/7 kể cả thứ 7, chủ nhật và ngày lễ, giúp mọi người tiết kiệm thời gian, dễ dàng tiếp cận dịch vụ tài chính văn minh, hiện đại; nhận tiền về tài khoản nhanh chóng. Lãi suất vay theo ngày, không theo chu kỳ; khách hàng có thể tự do lựa chọn khoản vay với chính sách ưu đãi hấp dẫn.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nTrong 1 th\u00e1ng c\u1ea7n gi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 6 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 1 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 5000000,
                        "code": null,
                        "price": 168000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 108,
                        "name": "TINVAY - Vay dễ dàng, Duyệt cấp tốc",
                        "description": "CICDATA là công ty hàng đầu trong lĩnh vực khai thác dữ liệu và là công ty duy nhất hợp tác với tât cả nhà mạng tại Việt Nam. Dựa trên nền tảng Dữ liệu lớn (Big data) và Máy học (Machine Learning), CIC DATA cung cấp các dịch vụ tiêu chuẩn cao trong việc chấm điểm tín dụng và tạo khách hàng vay tín chấp và thẻ tín dụng tiềm năng. TINVAY là 1 giải pháp được phát triển bởi CICDATA\nTINVAY là nền tảng kết nối và sơ duyệt khoản vay từ các ngân hàng và tổ chức tài chính lớn nhất Việt Nam. TINVAY ứng dụng công nghệ trí tuệ nhân tạo AI và dữ liệu lớn Big Data để tìm kiếm và phê duyệt tự động khoản vay phù hợp nhất cho khách hàng, mà không cần chứng minh tài chính hay thế chấp tài sản.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "CICDATA là công ty hàng đầu trong lĩnh vực khai thác dữ liệu và là công ty duy nhất hợp tác với tât cả nhà mạng tại Việt Nam. Dựa trên nền tảng Dữ liệu lớn (Big data) và Máy học (Machine Learning), CIC DATA cung cấp các dịch vụ tiêu chuẩn cao trong việc chấm điểm tín dụng và tạo khách hàng vay tín chấp và thẻ tín dụng tiềm năng. TINVAY là 1 giải pháp được phát triển bởi CICDATA\nTINVAY là nền tảng kết nối và sơ duyệt khoản vay từ các ngân hàng và tổ chức tài chính lớn nhất Việt Nam. TINVAY ứng dụng công nghệ trí tuệ nhân tạo AI và dữ liệu lớn Big Data để tìm kiếm và phê duyệt tự động khoản vay phù hợp nhất cho khách hàng, mà không cần chứng minh tài chính hay thế chấp tài sản.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 10 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 4 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 15000000,
                        "code": null,
                        "price": 500000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 109,
                        "name": "Tiền Ơi",
                        "description": "Cuối tháng tiền lương chưa có, bao nhiêu khoản phải lo? Bạn đã quá chán các thủ tục rườm rà đến từ ngân hàng. Tiền Ơi sẽ giúp bạn giải quyết các vấn đề về tài chính một cách đơn giản và dễ dàng nhất. Thủ tục hoàn toàn online, không mất phí tư vấn. Giải ngân ngay trong ngày.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Cuối tháng tiền lương chưa có, bao nhiêu khoản phải lo? Bạn đã quá chán các thủ tục rườm rà đến từ ngân hàng. Tiền Ơi sẽ giúp bạn giải quyết các vấn đề về tài chính một cách đơn giản và dễ dàng nhất. Thủ tục hoàn toàn online, không mất phí tư vấn. Giải ngân ngay trong ngày.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 8 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 3 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 10000000,
                        "code": null,
                        "price": 330000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 110,
                        "name": "FINDO",
                        "description": "Findo.vn là nền tảng tư vấn và cung cấp các giải pháp tài chính trực tuyến 24/7. Dịch vụ Findo.vn phục vụ đối tượng khách hàng là người Việt Nam trong độ tuổi từ 20 – 60 tuổi, có việc làm thu nhập ổn định. Vay tiền online với findo Rất đơn giản, bạn chỉ cần chuẩn bị Chứng minh nhân dân/ Thẻ Căn cước công dân và hình chân dung để gửi yêu cầu đăng ký hồ sơ.\nFindo.vn là nền tảng tư vấn và cung cấp các giải pháp tài chính trực tuyến 24/7, nhằm hỗ trợ cho các nhu cầu tài chính đột xuất của bạn.\nThấu hiểu được những vấn đề tài chính bạn đang gặp phải, chúng tôi cố gắng mang đến bạn những giải pháp tài chính đơn giản, nhanh chóng và thuận tiện nhất.\nCông ty TNHH SOFI SOLUTIONS là đơn vị quản lý và thực hiện hoạt động tư vấn, kết nối tài chính.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Findo.vn là nền tảng tư vấn và cung cấp các giải pháp tài chính trực tuyến 24/7. Dịch vụ Findo.vn phục vụ đối tượng khách hàng là người Việt Nam trong độ tuổi từ 20 – 60 tuổi, có việc làm thu nhập ổn định. Vay tiền online với findo Rất đơn giản, bạn chỉ cần chuẩn bị Chứng minh nhân dân/ Thẻ Căn cước công dân và hình chân dung để gửi yêu cầu đăng ký hồ sơ.\nFindo.vn là nền tảng tư vấn và cung cấp các giải pháp tài chính trực tuyến 24/7, nhằm hỗ trợ cho các nhu cầu tài chính đột xuất của bạn.\nThấu hiểu được những vấn đề tài chính bạn đang gặp phải, chúng tôi cố gắng mang đến bạn những giải pháp tài chính đơn giản, nhanh chóng và thuận tiện nhất.\nCông ty TNHH SOFI SOLUTIONS là đơn vị quản lý và thực hiện hoạt động tư vấn, kết nối tài chính.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 10 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 4 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 15000000,
                        "code": null,
                        "price": 500000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 111,
                        "name": "Avay.vn",
                        "description": "AVAY là giải pháp tài chính giúp kết nối người cần vay tới các tổ chức tài chính lớn và uy tín trên thị trường Việt Nam. Sử dụng công nghệ Big Data từ Trusting Social, một tổ chức quy tụ các giáo sư, tiến sĩ về khoa học dữ liệu và khoa học máy tính, tới từ các tổ chức hàng đầu thế giới như: SRI, IBM Research, Microsoft, Goldman Sachs, Vodafone, Barclays.\n\nAVAY áp dụng công nghệ tự động hóa trong việc đánh giá khả năng chi trả của người vay, gửi thông tin cho ngân hàng phù hợp và duyệt tự động các khoản vay.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "AVAY là giải pháp tài chính giúp kết nối người cần vay tới các tổ chức tài chính lớn và uy tín trên thị trường Việt Nam. Sử dụng công nghệ Big Data từ Trusting Social, một tổ chức quy tụ các giáo sư, tiến sĩ về khoa học dữ liệu và khoa học máy tính, tới từ các tổ chức hàng đầu thế giới như: SRI, IBM Research, Microsoft, Goldman Sachs, Vodafone, Barclays.\n\nAVAY áp dụng công nghệ tự động hóa trong việc đánh giá khả năng chi trả của người vay, gửi thông tin cho ngân hàng phù hợp và duyệt tự động các khoản vay.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK,ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 12 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 5 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 20000000,
                        "code": null,
                        "price": 660000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 112,
                        "name": "ATM Online CPS",
                        "description": "ATM Online là giải pháp tài chính trực tuyến ưu việt hàng đầu tại Việt Nam, với các ưu điểm như: nhanh chóng, tiện lợi, thanh toán linh hoạt và chi phí cực kỳ hợp lý. \n\nƯu điểm của ATM Online là khách hàng có thể ngay lập tức biết được mình được duyệt vay vốn hay không",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "ATM Online là giải pháp tài chính trực tuyến ưu việt hàng đầu tại Việt Nam, với các ưu điểm như: nhanh chóng, tiện lợi, thanh toán linh hoạt và chi phí cực kỳ hợp lý. \n\nƯu điểm của ATM Online là khách hàng có thể ngay lập tức biết được mình được duyệt vay vốn hay không",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 15 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 6 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 30000000,
                        "code": null,
                        "price": 1000000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 113,
                        "name": "VAY VND CPS",
                        "description": "VayVNĐ là nhà cung cấp dịch vụ tư vấn giải pháp tài chính và cung cấp giải pháp tiền mặt trực tuyến. Mỗi ngày, với sự giúp đỡ của chúng tôi, rất nhiều người trên cả nước đã giải quyết được các vấn đề tài chính khẩn cấp của mình với thủ tục đang ký đơn giản.\n\nNhiệm vụ của chúng tôi là trở thành một đối tác đáng tin cậy bằng cách cung cấp các giải pháp tài chính với điều khoản thuận lợi nhất cho những người vướng vào Hoàn Cảnh khó khăn về tài chính, làm cho thị trường cho vay ở Việt Nam trở nên vô cùng minh bạch, đơn giản với điều kiện phù hợp nhất đối với từng đối tượng khách hàng.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "VayVNĐ là nhà cung cấp dịch vụ tư vấn giải pháp tài chính và cung cấp giải pháp tiền mặt trực tuyến. Mỗi ngày, với sự giúp đỡ của chúng tôi, rất nhiều người trên cả nước đã giải quyết được các vấn đề tài chính khẩn cấp của mình với thủ tục đang ký đơn giản.\n\nNhiệm vụ của chúng tôi là trở thành một đối tác đáng tin cậy bằng cách cung cấp các giải pháp tài chính với điều khoản thuận lợi nhất cho những người vướng vào Hoàn Cảnh khó khăn về tài chính, làm cho thị trường cho vay ở Việt Nam trở nên vô cùng minh bạch, đơn giản với điều kiện phù hợp nhất đối với từng đối tượng khách hàng.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK,ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 12 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 5 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 20000000,
                        "code": null,
                        "price": 660000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 114,
                        "name": "Vay Quá Dễ",
                        "description": "Vay Quá Dễ là dịch vụ miễn phí lựa chọn tức thời các khoản vay 0%. Dịch vụ Vay Quá Dễ nghiên cứu các đề xuất từ những công ty đáp ứng 4 tiêu chí: không có các khoản thanh toán ẩn, uy tín được kiểm chứng, cấp tín dụng trực tuyến và bảo vệ dữ liệu cá nhân.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Vay Quá Dễ là dịch vụ miễn phí lựa chọn tức thời các khoản vay 0%. Dịch vụ Vay Quá Dễ nghiên cứu các đề xuất từ những công ty đáp ứng 4 tiêu chí: không có các khoản thanh toán ẩn, uy tín được kiểm chứng, cấp tín dụng trực tuyến và bảo vệ dữ liệu cá nhân.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 10 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 4 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 15000000,
                        "code": null,
                        "price": 500000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 2,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 115,
                        "name": "Vietravel - Đặt tour du lịch trọn gói tại Việt Nam và Khắp thế Giới",
                        "description": "Travel.com.vn là Website thương mại điện tử số 1 trong ngành Du lịch Việt Nam. Đây là hệ thống đặt Tour trực tuyến nhanh gọn, dễ dàng, thuận tiện và tiết kiệm thời gian. \n\nVới tần suất khởi hành đều đặn quanh năm, Vietravel luôn đáp ứng được mọi nhu cầu du lịch trong và ngoài nước của khách hàng. Tỷ lệ chia sẻ theo từng nhóm Tour hấp dẫn lên đến 1,050,000 VNĐ/Tour/Khách.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Travel.com.vn là Website thương mại điện tử số 1 trong ngành Du lịch Việt Nam. Đây là hệ thống đặt Tour trực tuyến nhanh gọn, dễ dàng, thuận tiện và tiết kiệm thời gian. \n\nVới tần suất khởi hành đều đặn quanh năm, Vietravel luôn đáp ứng được mọi nhu cầu du lịch trong và ngoài nước của khách hàng. Tỷ lệ chia sẻ theo từng nhóm Tour hấp dẫn lên đến 1,050,000 VNĐ/Tour/Khách.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK,ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 12 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 5 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 20000000,
                        "code": null,
                        "price": 660000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 116,
                        "name": "Vexere - Đặt Vé Xe Online",
                        "description": "VeXeRe.com là hệ thống vé xe lớn nhất Việt Nam, với hơn 550 hãng xe hợp tác bán vé với VeXeRe, phủ rộng hơn 2600 tuyến đường trong và ngoài nước giúp người dùng có thể tìm thông tin chuyến xe, hãng xe, và mua vé trực tuyến dễ dàng. Đồng thời, VeXeRe.com còn cung cấp những đánh giá của các hành khách đã đi những hãng xe này. Hành khách có thể dễ dàng lựa chọn trước chỗ ngồi yêu thích, thanh toán vé trực tuyến, tiền mặt tại các cửa hàng tiện lợi trên cả nước.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "VeXeRe.com là hệ thống vé xe lớn nhất Việt Nam, với hơn 550 hãng xe hợp tác bán vé với VeXeRe, phủ rộng hơn 2600 tuyến đường trong và ngoài nước giúp người dùng có thể tìm thông tin chuyến xe, hãng xe, và mua vé trực tuyến dễ dàng. Đồng thời, VeXeRe.com còn cung cấp những đánh giá của các hành khách đã đi những hãng xe này. Hành khách có thể dễ dàng lựa chọn trước chỗ ngồi yêu thích, thanh toán vé trực tuyến, tiền mặt tại các cửa hàng tiện lợi trên cả nước.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 10 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 4 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 15000000,
                        "code": null,
                        "price": 500000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 117,
                        "name": "{\"en\":\"Bamboo AirwaysTRAVEL and LEISURE\",\"vi\":\"Bamboo AirwaysTRAVEL and LEISURE\",\"cn\":\"Bamboo AirwaysTRAVEL and LEISURE\"}",
                        "description": "{\"en\":\"<p>Bamboo Airways l&agrave; m\u1ed9t h&atilde;ng h&agrave;ng kh&ocirc;ng Starup trong n\u01b0\u1edbc, d\u01b0\u1edbi s\u1ef1 \u0111i\u1ec1u h&agrave;nh v&agrave; qu\u1ea3n l&yacute; c\u1ee7a t\u1eadp \u0111o&agrave;n FLC. Bamboo Airways h\u1ee9a h\u1eb9n s\u1ea3i c&aacute;nh, tr\u1edf th&agrave;nh m\u1ed9t h&atilde;ng h&agrave;ng kh&ocirc;ng khai th&aacute;c c&aacute;c chuy\u1ebfn bay n\u1ed9i \u0111\u1ecba ch\u1ea5t l\u01b0\u1ee3ng cao, mang l\u1ea1i \u0111\u01b0\u1ee3c cho h&agrave;nh kh&aacute;ch nh\u1eefng h&agrave;nh tr&igrave;nh du l\u1ecbch tuy\u1ec7t v\u1eddi.<\\/p>\",\"vi\":\"<p>Bamboo Airways l&agrave; m\u1ed9t h&atilde;ng h&agrave;ng kh&ocirc;ng Starup trong n\u01b0\u1edbc, d\u01b0\u1edbi s\u1ef1 \u0111i\u1ec1u h&agrave;nh v&agrave; qu\u1ea3n l&yacute; c\u1ee7a t\u1eadp \u0111o&agrave;n FLC. Bamboo Airways h\u1ee9a h\u1eb9n s\u1ea3i c&aacute;nh, tr\u1edf th&agrave;nh m\u1ed9t h&atilde;ng h&agrave;ng kh&ocirc;ng khai th&aacute;c c&aacute;c chuy\u1ebfn bay n\u1ed9i \u0111\u1ecba ch\u1ea5t l\u01b0\u1ee3ng cao, mang l\u1ea1i \u0111\u01b0\u1ee3c cho h&agrave;nh kh&aacute;ch nh\u1eefng h&agrave;nh tr&igrave;nh du l\u1ecbch tuy\u1ec7t v\u1eddi.<\\/p>\",\"cn\":\"<p>Bamboo Airways l&agrave; m\u1ed9t h&atilde;ng h&agrave;ng kh&ocirc;ng Starup trong n\u01b0\u1edbc, d\u01b0\u1edbi s\u1ef1 \u0111i\u1ec1u h&agrave;nh v&agrave; qu\u1ea3n l&yacute; c\u1ee7a t\u1eadp \u0111o&agrave;n FLC. Bamboo Airways h\u1ee9a h\u1eb9n s\u1ea3i c&aacute;nh, tr\u1edf th&agrave;nh m\u1ed9t h&atilde;ng h&agrave;ng kh&ocirc;ng khai th&aacute;c c&aacute;c chuy\u1ebfn bay n\u1ed9i \u0111\u1ecba ch\u1ea5t l\u01b0\u1ee3ng cao, mang l\u1ea1i \u0111\u01b0\u1ee3c cho h&agrave;nh kh&aacute;ch nh\u1eefng h&agrave;nh tr&igrave;nh du l\u1ecbch tuy\u1ec7t v\u1eddi.<\\/p>\"}",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "{\"en\":\"Bamboo Airways l\u00e0 m\u1ed9t h\u00e3ng h\u00e0ng kh\u00f4ng Starup trong n\u01b0\u1edbc, d\u01b0\u1edbi s\u1ef1 \u0111i\u1ec1u h\u00e0nh v\u00e0 qu\u1ea3n l\u00fd c\u1ee7a t\u1eadp \u0111o\u00e0n FLC. Bamboo Airways h\u1ee9a h\u1eb9n s\u1ea3i c\u00e1nh, tr\u1edf th\u00e0nh m\u1ed9t h\u00e3ng h\u00e0ng kh\u00f4ng khai th\u00e1c c\u00e1c chuy\u1ebfn bay n\u1ed9i \u0111\u1ecba ch\u1ea5t l\u01b0\u1ee3ng cao, mang l\u1ea1i \u0111\u01b0\u1ee3c cho h\u00e0nh kh\u00e1ch nh\u1eefng h\u00e0nh tr\u00ecnh du l\u1ecbch tuy\u1ec7t v\u1eddi.\",\"vi\":\"Bamboo Airways l\u00e0 m\u1ed9t h\u00e3ng h\u00e0ng kh\u00f4ng Starup trong n\u01b0\u1edbc, d\u01b0\u1edbi s\u1ef1 \u0111i\u1ec1u h\u00e0nh v\u00e0 qu\u1ea3n l\u00fd c\u1ee7a t\u1eadp \u0111o\u00e0n FLC. Bamboo Airways h\u1ee9a h\u1eb9n s\u1ea3i c\u00e1nh, tr\u1edf th\u00e0nh m\u1ed9t h\u00e3ng h\u00e0ng kh\u00f4ng khai th\u00e1c c\u00e1c chuy\u1ebfn bay n\u1ed9i \u0111\u1ecba ch\u1ea5t l\u01b0\u1ee3ng cao, mang l\u1ea1i \u0111\u01b0\u1ee3c cho h\u00e0nh kh\u00e1ch nh\u1eefng h\u00e0nh tr\u00ecnh du l\u1ecbch tuy\u1ec7t v\u1eddi.\",\"cn\":\"Bamboo Airways l\u00e0 m\u1ed9t h\u00e3ng h\u00e0ng kh\u00f4ng Starup trong n\u01b0\u1edbc, d\u01b0\u1edbi s\u1ef1 \u0111i\u1ec1u h\u00e0nh v\u00e0 qu\u1ea3n l\u00fd c\u1ee7a t\u1eadp \u0111o\u00e0n FLC. Bamboo Airways h\u1ee9a h\u1eb9n s\u1ea3i c\u00e1nh, tr\u1edf th\u00e0nh m\u1ed9t h\u00e3ng h\u00e0ng kh\u00f4ng khai th\u00e1c c\u00e1c chuy\u1ebfn bay n\u1ed9i \u0111\u1ecba ch\u1ea5t l\u01b0\u1ee3ng cao, mang l\u1ea1i \u0111\u01b0\u1ee3c cho h\u00e0nh kh\u00e1ch nh\u1eefng h\u00e0nh tr\u00ecnh du l\u1ecbch tuy\u1ec7t v\u1eddi.\"}",
                        "link_": null,
                        "image": "{\"en\":\"\\/upload\\/images\\/A\\/B\\/C\\/photo_2023-03-03_16-06-23.jpg\",\"vi\":null,\"cn\":null}",
                        "list_task": "{\"en\":[null],\"vi\":[null],\"cn\":[null]}",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 50000000,
                        "code": null,
                        "price": 1680000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 16:10:26",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 118,
                        "name": "Traveloka CPS",
                        "description": "Traveloka là một công ty kỳ lân có trụ sở tại Indonesia, chuyên cung cấp dịch vụ đặt vé máy bay và đặt phòng khách sạn trực tuyến, hiện đang mở rộng nhanh chóng sang Đông Nam Á và Úc. Trong thời gian gần đây, Traveloka đã mở rộng để cung cấp các sản phẩm và dịch vụ phong cách sống, chẳng hạn như vé tham quan, các hoạt động giải trí, cho thuê xe hơi, đưa đón sân bay, đặt bàn nhà hàng và phiếu mua hàng.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Traveloka là một công ty kỳ lân có trụ sở tại Indonesia, chuyên cung cấp dịch vụ đặt vé máy bay và đặt phòng khách sạn trực tuyến, hiện đang mở rộng nhanh chóng sang Đông Nam Á và Úc. Trong thời gian gần đây, Traveloka đã mở rộng để cung cấp các sản phẩm và dịch vụ phong cách sống, chẳng hạn như vé tham quan, các hoạt động giải trí, cho thuê xe hơi, đưa đón sân bay, đặt bàn nhà hàng và phiếu mua hàng.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 15 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 6 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 30000000,
                        "code": null,
                        "price": 1000000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 119,
                        "name": "Klook - SIM, Vé Tham Quan, Show, Tour, Vận Chuyển Trên 30 Quốc Gia",
                        "description": "Klook Việt Nam - Khám phá và đặt trước các hoạt động du lịch đặc sắc với giá độc quyền \n- Klook đem đến cho bạn giải pháp thật đơn giản để khám phá các điểm tham quan và hoạt động du lịch \n- Khám phá và đặt dịch vụ tại điểm đến với giá tốt nhất. \n- Tiết kiệm từ 10%-50% so với mua vé trực tiếp \n- Không lo hết chỗ, không xếp hàng mua vé, đưa đón tận nơi. ",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Klook Việt Nam - Khám phá và đặt trước các hoạt động du lịch đặc sắc với giá độc quyền \n- Klook đem đến cho bạn giải pháp thật đơn giản để khám phá các điểm tham quan và hoạt động du lịch \n- Khám phá và đặt dịch vụ tại điểm đến với giá tốt nhất. \n- Tiết kiệm từ 10%-50% so với mua vé trực tiếp \n- Không lo hết chỗ, không xếp hàng mua vé, đưa đón tận nơi. ",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 10 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 4 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 15000000,
                        "code": null,
                        "price": 500000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 120,
                        "name": "Mioto - Website - Thuê xe Tự Lái & Có tài xế",
                        "description": "MIOTO là ứng dụng thuê xe (tự lái + có tài xế) mới mẻ và hiện đại, cung cấp giải pháp kết nối khách thuê với hàng ngàn chủ xe theo cách nhanh chóng, an toàn và tiết kiệm nhằm mang lại cho bạn những trải nghiệm thực sự khác biệt so với các dịch vụ cho thuê xe truyền thống đang có trên thị trường.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "MIOTO là ứng dụng thuê xe (tự lái + có tài xế) mới mẻ và hiện đại, cung cấp giải pháp kết nối khách thuê với hàng ngàn chủ xe theo cách nhanh chóng, an toàn và tiết kiệm nhằm mang lại cho bạn những trải nghiệm thực sự khác biệt so với các dịch vụ cho thuê xe truyền thống đang có trên thị trường.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK,ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 12 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 5 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 20000000,
                        "code": null,
                        "price": 660000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 121,
                        "name": "Việt Nam Booking",
                        "description": "Việt Nam Booking - Đại lý chính thức các hãng hàng không tại Việt Nam.\nHiện tại, Việt Nam Booking đang liên kết lại với ACCESSTRADE với mức commission mới bắt đầu từ tháng 11.2020, cùng một website chỉnh chủ, chạy mượt mà hơn rất nhiều. Cùng với rất nhiều dịch vụ đang chờ Pub đẩy số cùng:\n- Vé máy bay\n- Du lịch\n- Khách sạn\n- Visa\n- Combo\n->>> Tổng hợp liên tục căc tuyến hot, vé máy bay giảm mạnh tại đây: https://bit.ly/34FJ9LD",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Việt Nam Booking - Đại lý chính thức các hãng hàng không tại Việt Nam.\nHiện tại, Việt Nam Booking đang liên kết lại với ACCESSTRADE với mức commission mới bắt đầu từ tháng 11.2020, cùng một website chỉnh chủ, chạy mượt mà hơn rất nhiều. Cùng với rất nhiều dịch vụ đang chờ Pub đẩy số cùng:\n- Vé máy bay\n- Du lịch\n- Khách sạn\n- Visa\n- Combo\n->>> Tổng hợp liên tục căc tuyến hot, vé máy bay giảm mạnh tại đây: https://bit.ly/34FJ9LD",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 8 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 3 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 10000000,
                        "code": null,
                        "price": 330000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 122,
                        "name": "GOTADI – Vé máy bay giá rẻ, uy tín",
                        "description": "Gotadi.com là trang mạng tìm kiếm, đặt vé máy bay, khách sạn, và thanh toán hoàn toàn trực tuyến trên 900 hãng hàng không toàn cầu, với hơn 2.000 khách sạn trong nước, 400.000 khách sạn quốc tế, cùng các dịch vụ du lịch đa dạng. \n\nMục tiêu phát triển của Gotadi.com không chỉ là mạng du lịch toàn diện trực tuyến, mà sẽ trở thành một OTA (Online Travel Agent) Việt Nam tiêu chuẩn quốc tế. Bên cạnh việc hoàn thiện hệ thống One stop shop, giúp khách hàng tiết kiệm thời gian tìm kiếm thông tin trên nhiều trang mạng khác nhau, Gotadi.com đang không ngừng mở rộng các kênh đại lý. \n\nĐến với Gotadi, Quý khách hàng sẽ có cơ hội đặt vé máy bay và khách sạn với chi phí rẻ nhất cũng như trải nghiệm dịch vụ tuyệt vời từ đội ngũ chăm sóc khách hàng tận tình và chuyên nghiệp.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Gotadi.com là trang mạng tìm kiếm, đặt vé máy bay, khách sạn, và thanh toán hoàn toàn trực tuyến trên 900 hãng hàng không toàn cầu, với hơn 2.000 khách sạn trong nước, 400.000 khách sạn quốc tế, cùng các dịch vụ du lịch đa dạng. \n\nMục tiêu phát triển của Gotadi.com không chỉ là mạng du lịch toàn diện trực tuyến, mà sẽ trở thành một OTA (Online Travel Agent) Việt Nam tiêu chuẩn quốc tế. Bên cạnh việc hoàn thiện hệ thống One stop shop, giúp khách hàng tiết kiệm thời gian tìm kiếm thông tin trên nhiều trang mạng khác nhau, Gotadi.com đang không ngừng mở rộng các kênh đại lý. \n\nĐến với Gotadi, Quý khách hàng sẽ có cơ hội đặt vé máy bay và khách sạn với chi phí rẻ nhất cũng như trải nghiệm dịch vụ tuyệt vời từ đội ngũ chăm sóc khách hàng tận tình và chuyên nghiệp.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK,ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 12 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 5 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 20000000,
                        "code": null,
                        "price": 660000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 123,
                        "name": "ATADI _ Đặt vé máy bay trực tuyến giá rẻ",
                        "description": "Atadi.vn là một trong những website săn vé rẻ đầu tiên tại Việt Nam giúp người dùng tìm kiếm, so sánh và đặt vé máy bay trực tuyến của nhiều Hãng hàng không trên cùng một giao diện web. Được xây dựng bởi đội ngũ các Chuyên gia Công nghệ – tự động hoá có tâm huyết, đam mê cùng nhiều năm kinh nghiệm trong lĩnh vực hàng không, du lịch và thương mại điện tử.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Atadi.vn là một trong những website săn vé rẻ đầu tiên tại Việt Nam giúp người dùng tìm kiếm, so sánh và đặt vé máy bay trực tuyến của nhiều Hãng hàng không trên cùng một giao diện web. Được xây dựng bởi đội ngũ các Chuyên gia Công nghệ – tự động hoá có tâm huyết, đam mê cùng nhiều năm kinh nghiệm trong lĩnh vực hàng không, du lịch và thương mại điện tử.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 8 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 3 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 10000000,
                        "code": null,
                        "price": 330000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 124,
                        "name": "BestPrice - Đại lý đặt phòng khách sạn, vé máy bay, tour du lịch trực tuyến giá tốt nhất",
                        "description": "BestPrice là một trong những công ty du lịch uy tín hàng đầu Việt Nam, cung cấp dịch vụ đặt phòng khách sạn, đặt vé máy bay, tour du lịch cho khách trong nước và Quốc tế, được kết hợp thông minh và tối ưu cho phép khách hàng đặt những chuyến đi hoàn hảo với giá tốt nhất. \nCác Publishers cùng nâng cao doanh thu hoa hồng cùng với chiến dịch Best Price nào!\n\nBestPrice ra mắt dịch vụ mới mang tên voucher VNV - gói voucher nghỉ dưỡng gồm combo Vé máy bay khứ hồi Vietnam Airlines + Phòng Vinpearl 3N2Đ. Đây là gói voucher BestPrice phân phối độc quyền.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "BestPrice là một trong những công ty du lịch uy tín hàng đầu Việt Nam, cung cấp dịch vụ đặt phòng khách sạn, đặt vé máy bay, tour du lịch cho khách trong nước và Quốc tế, được kết hợp thông minh và tối ưu cho phép khách hàng đặt những chuyến đi hoàn hảo với giá tốt nhất. \nCác Publishers cùng nâng cao doanh thu hoa hồng cùng với chiến dịch Best Price nào!\n\nBestPrice ra mắt dịch vụ mới mang tên voucher VNV - gói voucher nghỉ dưỡng gồm combo Vé máy bay khứ hồi Vietnam Airlines + Phòng Vinpearl 3N2Đ. Đây là gói voucher BestPrice phân phối độc quyền.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nTrong 1 th\u00e1ng c\u1ea7n gi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 6 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 1 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 5000000,
                        "code": null,
                        "price": 168000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 125,
                        "name": "{\"en\":\"PYS TRAVEL\",\"vi\":\"PYS TRAVEL\",\"cn\":\"PYS TRAVEL\"}",
                        "description": "{\"en\":\"<p>\u0110\u01b0\u1ee3c th&agrave;nh l\u1eadp v&agrave; v\u1eadn h&agrave;nh b\u1edfi nh\u1eefng ng\u01b0\u1eddi tr\u1ebb, n\u0103ng \u0111\u1ed9ng, lu&ocirc;n t&igrave;m t&ograve;i nh\u1eefng xu h\u01b0\u1edbng du l\u1ecbch m\u1edbi v&agrave; kh&ocirc;ng ng\u1eebng n&acirc;ng cao ch\u1ea5t l\u01b0\u1ee3ng d\u1ecbch v\u1ee5 nh\u1eb1m mang l\u1ea1i cho kh&aacute;ch h&agrave;ng s\u1ef1 h&agrave;i l&ograve;ng v&agrave; nh\u1eefng tr\u1ea3i nghi\u1ec7m l&yacute; th&uacute; trong m\u1ed7i chuy\u1ebfn \u0111i. &quot;Ti&ecirc;n Phong&quot; l&agrave; kim ch\u1ec9 nam cho m\u1ecdi ho\u1ea1t \u0111\u1ed9ng c\u1ee7a PYS Travel. \u0110&oacute; c\u0169ng l&agrave; l&yacute; do v&igrave; sao PYS Travel ch\u1ecdn h&igrave;nh c&aacute;nh bu\u1ed3n l&agrave;m logo \u0111\u1ec3 bi\u1ec3u tr\u01b0ng cho t&iacute;nh c&aacute;ch c\u1ee7a m&igrave;nh. Ch&uacute;ng t&ocirc;i lu&ocirc;n t&igrave;m t&ograve;i nh\u1eefng \u0111\u1ecba \u0111i\u1ec3m du l\u1ecbch ho&agrave;n to&agrave;n m\u1edbi m\u1ebb; c\u0169ng nh\u01b0 x&acirc;y d\u1ef1ng c&aacute;c d\u1ecbch v\u1ee5 gi&aacute; tr\u1ecb gia t\u0103ng \u0111\u1ed9c \u0111&aacute;o, h\u1ea5p d\u1eabn.<\\/p>\",\"vi\":\"<p>\u0110\u01b0\u1ee3c th&agrave;nh l\u1eadp v&agrave; v\u1eadn h&agrave;nh b\u1edfi nh\u1eefng ng\u01b0\u1eddi tr\u1ebb, n\u0103ng \u0111\u1ed9ng, lu&ocirc;n t&igrave;m t&ograve;i nh\u1eefng xu h\u01b0\u1edbng du l\u1ecbch m\u1edbi v&agrave; kh&ocirc;ng ng\u1eebng n&acirc;ng cao ch\u1ea5t l\u01b0\u1ee3ng d\u1ecbch v\u1ee5 nh\u1eb1m mang l\u1ea1i cho kh&aacute;ch h&agrave;ng s\u1ef1 h&agrave;i l&ograve;ng v&agrave; nh\u1eefng tr\u1ea3i nghi\u1ec7m l&yacute; th&uacute; trong m\u1ed7i chuy\u1ebfn \u0111i. &quot;Ti&ecirc;n Phong&quot; l&agrave; kim ch\u1ec9 nam cho m\u1ecdi ho\u1ea1t \u0111\u1ed9ng c\u1ee7a PYS Travel. \u0110&oacute; c\u0169ng l&agrave; l&yacute; do v&igrave; sao PYS Travel ch\u1ecdn h&igrave;nh c&aacute;nh bu\u1ed3n l&agrave;m logo \u0111\u1ec3 bi\u1ec3u tr\u01b0ng cho t&iacute;nh c&aacute;ch c\u1ee7a m&igrave;nh. Ch&uacute;ng t&ocirc;i lu&ocirc;n t&igrave;m t&ograve;i nh\u1eefng \u0111\u1ecba \u0111i\u1ec3m du l\u1ecbch ho&agrave;n to&agrave;n m\u1edbi m\u1ebb; c\u0169ng nh\u01b0 x&acirc;y d\u1ef1ng c&aacute;c d\u1ecbch v\u1ee5 gi&aacute; tr\u1ecb gia t\u0103ng \u0111\u1ed9c \u0111&aacute;o, h\u1ea5p d\u1eabn.<\\/p>\",\"cn\":\"<p>\u0110\u01b0\u1ee3c th&agrave;nh l\u1eadp v&agrave; v\u1eadn h&agrave;nh b\u1edfi nh\u1eefng ng\u01b0\u1eddi tr\u1ebb, n\u0103ng \u0111\u1ed9ng, lu&ocirc;n t&igrave;m t&ograve;i nh\u1eefng xu h\u01b0\u1edbng du l\u1ecbch m\u1edbi v&agrave; kh&ocirc;ng ng\u1eebng n&acirc;ng cao ch\u1ea5t l\u01b0\u1ee3ng d\u1ecbch v\u1ee5 nh\u1eb1m mang l\u1ea1i cho kh&aacute;ch h&agrave;ng s\u1ef1 h&agrave;i l&ograve;ng v&agrave; nh\u1eefng tr\u1ea3i nghi\u1ec7m l&yacute; th&uacute; trong m\u1ed7i chuy\u1ebfn \u0111i. &quot;Ti&ecirc;n Phong&quot; l&agrave; kim ch\u1ec9 nam cho m\u1ecdi ho\u1ea1t \u0111\u1ed9ng c\u1ee7a PYS Travel. \u0110&oacute; c\u0169ng l&agrave; l&yacute; do v&igrave; sao PYS Travel ch\u1ecdn h&igrave;nh c&aacute;nh bu\u1ed3n l&agrave;m logo \u0111\u1ec3 bi\u1ec3u tr\u01b0ng cho t&iacute;nh c&aacute;ch c\u1ee7a m&igrave;nh. Ch&uacute;ng t&ocirc;i lu&ocirc;n t&igrave;m t&ograve;i nh\u1eefng \u0111\u1ecba \u0111i\u1ec3m du l\u1ecbch ho&agrave;n to&agrave;n m\u1edbi m\u1ebb; c\u0169ng nh\u01b0 x&acirc;y d\u1ef1ng c&aacute;c d\u1ecbch v\u1ee5 gi&aacute; tr\u1ecb gia t\u0103ng \u0111\u1ed9c \u0111&aacute;o, h\u1ea5p d\u1eabn.<\\/p>\"}",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "{\"en\":\"\u0110\u01b0\u1ee3c th\u00e0nh l\u1eadp v\u00e0 v\u1eadn h\u00e0nh b\u1edfi nh\u1eefng ng\u01b0\u1eddi tr\u1ebb, n\u0103ng \u0111\u1ed9ng, lu\u00f4n t\u00ecm t\u00f2i nh\u1eefng xu h\u01b0\u1edbng du l\u1ecbch m\u1edbi v\u00e0 kh\u00f4ng ng\u1eebng n\u00e2ng cao ch\u1ea5t l\u01b0\u1ee3ng d\u1ecbch v\u1ee5 nh\u1eb1m mang l\u1ea1i cho kh\u00e1ch h\u00e0ng s\u1ef1 h\u00e0i l\u00f2ng v\u00e0 nh\u1eefng tr\u1ea3i nghi\u1ec7m l\u00fd th\u00fa trong m\u1ed7i chuy\u1ebfn \u0111i. &amp;quot;Ti\u00ean Phong&amp;quot; l\u00e0 kim ch\u1ec9 nam cho m\u1ecdi ho\u1ea1t \u0111\u1ed9ng c\u1ee7a PYS Travel. \u0110\u00f3 c\u0169ng l\u00e0 l\u00fd do v\u00ec sao PYS Travel ch\u1ecdn h\u00ecnh c\u00e1nh bu\u1ed3n l\u00e0m logo \u0111\u1ec3 bi\u1ec3u tr\u01b0ng cho t\u00ednh c\u00e1ch c\u1ee7a m\u00ecnh. Ch\u00fang t\u00f4i lu\u00f4n t\u00ecm t\u00f2i nh\u1eefng \u0111\u1ecba \u0111i\u1ec3m du l\u1ecbch ho\u00e0n to\u00e0n m\u1edbi m\u1ebb; c\u0169ng nh\u01b0 x\u00e2y d\u1ef1ng c\u00e1c d\u1ecbch v\u1ee5 gi\u00e1 tr\u1ecb gia t\u0103ng \u0111\u1ed9c \u0111\u00e1o, h\u1ea5p d\u1eabn.\",\"vi\":\"\u0110\u01b0\u1ee3c th\u00e0nh l\u1eadp v\u00e0 v\u1eadn h\u00e0nh b\u1edfi nh\u1eefng ng\u01b0\u1eddi tr\u1ebb, n\u0103ng \u0111\u1ed9ng, lu\u00f4n t\u00ecm t\u00f2i nh\u1eefng xu h\u01b0\u1edbng du l\u1ecbch m\u1edbi v\u00e0 kh\u00f4ng ng\u1eebng n\u00e2ng cao ch\u1ea5t l\u01b0\u1ee3ng d\u1ecbch v\u1ee5 nh\u1eb1m mang l\u1ea1i cho kh\u00e1ch h\u00e0ng s\u1ef1 h\u00e0i l\u00f2ng v\u00e0 nh\u1eefng tr\u1ea3i nghi\u1ec7m l\u00fd th\u00fa trong m\u1ed7i chuy\u1ebfn \u0111i. &amp;quot;Ti\u00ean Phong&amp;quot; l\u00e0 kim ch\u1ec9 nam cho m\u1ecdi ho\u1ea1t \u0111\u1ed9ng c\u1ee7a PYS Travel. \u0110\u00f3 c\u0169ng l\u00e0 l\u00fd do v\u00ec sao PYS Travel ch\u1ecdn h\u00ecnh c\u00e1nh bu\u1ed3n l\u00e0m logo \u0111\u1ec3 bi\u1ec3u tr\u01b0ng cho t\u00ednh c\u00e1ch c\u1ee7a m\u00ecnh. Ch\u00fang t\u00f4i lu\u00f4n t\u00ecm t\u00f2i nh\u1eefng \u0111\u1ecba \u0111i\u1ec3m du l\u1ecbch ho\u00e0n to\u00e0n m\u1edbi m\u1ebb; c\u0169ng nh\u01b0 x\u00e2y d\u1ef1ng c\u00e1c d\u1ecbch v\u1ee5 gi\u00e1 tr\u1ecb gia t\u0103ng \u0111\u1ed9c \u0111\u00e1o, h\u1ea5p d\u1eabn.\",\"cn\":\"\u0110\u01b0\u1ee3c th\u00e0nh l\u1eadp v\u00e0 v\u1eadn h\u00e0nh b\u1edfi nh\u1eefng ng\u01b0\u1eddi tr\u1ebb, n\u0103ng \u0111\u1ed9ng, lu\u00f4n t\u00ecm t\u00f2i nh\u1eefng xu h\u01b0\u1edbng du l\u1ecbch m\u1edbi v\u00e0 kh\u00f4ng ng\u1eebng n\u00e2ng cao ch\u1ea5t l\u01b0\u1ee3ng d\u1ecbch v\u1ee5 nh\u1eb1m mang l\u1ea1i cho kh\u00e1ch h\u00e0ng s\u1ef1 h\u00e0i l\u00f2ng v\u00e0 nh\u1eefng tr\u1ea3i nghi\u1ec7m l\u00fd th\u00fa trong m\u1ed7i chuy\u1ebfn \u0111i. &amp;quot;Ti\u00ean Phong&amp;quot; l\u00e0 kim ch\u1ec9 nam cho m\u1ecdi ho\u1ea1t \u0111\u1ed9ng c\u1ee7a PYS Travel. \u0110\u00f3 c\u0169ng l\u00e0 l\u00fd do v\u00ec sao PYS Travel ch\u1ecdn h\u00ecnh c\u00e1nh bu\u1ed3n l\u00e0m logo \u0111\u1ec3 bi\u1ec3u tr\u01b0ng cho t\u00ednh c\u00e1ch c\u1ee7a m\u00ecnh. Ch\u00fang t\u00f4i lu\u00f4n t\u00ecm t\u00f2i nh\u1eefng \u0111\u1ecba \u0111i\u1ec3m du l\u1ecbch ho\u00e0n to\u00e0n m\u1edbi m\u1ebb; c\u0169ng nh\u01b0 x\u00e2y d\u1ef1ng c\u00e1c d\u1ecbch v\u1ee5 gi\u00e1 tr\u1ecb gia t\u0103ng \u0111\u1ed9c \u0111\u00e1o, h\u1ea5p d\u1eabn.\"}",
                        "link_": null,
                        "image": "{\"en\":\"\\/upload\\/images\\/A\\/B\\/C\\/photo_2023-03-03_16-06-51.jpg\",\"vi\":\"{&quot;en&quot;:&quot;\\\\/upload\\\\/images\\\\/A\\\\/B\\\\/C\\\\/photo_2023-03-03_16-06-51.jpg&quot;,&quot;vi&quot;:null,&quot;cn&quot;:null}\",\"cn\":\"{&quot;en&quot;:&quot;\\\\/upload\\\\/images\\\\/A\\\\/B\\\\/C\\\\/photo_2023-03-03_16-06-51.jpg&quot;,&quot;vi&quot;:null,&quot;cn&quot;:null}\"}",
                        "list_task": "{\"en\":[null],\"vi\":[null],\"cn\":[null]}",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 50000000,
                        "code": null,
                        "price": 1680000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 16:11:37",
                        "category": 7,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 126,
                        "name": "{\"en\":\"TENTEN VN\",\"vi\":\"TENTEN VN\",\"cn\":\"TENTEN VN\"}",
                        "description": "{\"en\":\"<p>Tenten.vn (\u0111\u1ea1i l&yacute; duy nh\u1ea5t t\u1ea1i Vi\u1ec7t Nam c\u1ee7a Onamae.com-GMO Internet Inc, nh&agrave; \u0111\u0103ng k&yacute; t&ecirc;n mi\u1ec1n s\u1ed1 1 Nh\u1eadt B\u1ea3n v&agrave; h&agrave;ng \u0111\u1ea7u th\u1ebf gi\u1edbi) cung c\u1ea5p c&aacute;c d\u1ecbch v\u1ee5 li&ecirc;n quan \u0111\u1ebfn l\u0129nh v\u1ef1c t&ecirc;n mi\u1ec1n, hosting, VPS, SSL, email server, v&agrave; thi\u1ebft k\u1ebf website.<\\/p>\",\"vi\":\"<p>Tenten.vn (\u0111\u1ea1i l&yacute; duy nh\u1ea5t t\u1ea1i Vi\u1ec7t Nam c\u1ee7a Onamae.com-GMO Internet Inc, nh&agrave; \u0111\u0103ng k&yacute; t&ecirc;n mi\u1ec1n s\u1ed1 1 Nh\u1eadt B\u1ea3n v&agrave; h&agrave;ng \u0111\u1ea7u th\u1ebf gi\u1edbi) cung c\u1ea5p c&aacute;c d\u1ecbch v\u1ee5 li&ecirc;n quan \u0111\u1ebfn l\u0129nh v\u1ef1c t&ecirc;n mi\u1ec1n, hosting, VPS, SSL, email server, v&agrave; thi\u1ebft k\u1ebf website.<\\/p>\",\"cn\":\"<p>Tenten.vn (\u0111\u1ea1i l&yacute; duy nh\u1ea5t t\u1ea1i Vi\u1ec7t Nam c\u1ee7a Onamae.com-GMO Internet Inc, nh&agrave; \u0111\u0103ng k&yacute; t&ecirc;n mi\u1ec1n s\u1ed1 1 Nh\u1eadt B\u1ea3n v&agrave; h&agrave;ng \u0111\u1ea7u th\u1ebf gi\u1edbi) cung c\u1ea5p c&aacute;c d\u1ecbch v\u1ee5 li&ecirc;n quan \u0111\u1ebfn l\u0129nh v\u1ef1c t&ecirc;n mi\u1ec1n, hosting, VPS, SSL, email server, v&agrave; thi\u1ebft k\u1ebf website.<\\/p>\"}",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "{\"en\":\"Tenten.vn (\u0111\u1ea1i l\u00fd duy nh\u1ea5t t\u1ea1i Vi\u1ec7t Nam c\u1ee7a Onamae.com-GMO Internet Inc, nh\u00e0 \u0111\u0103ng k\u00fd t\u00ean mi\u1ec1n s\u1ed1 1 Nh\u1eadt B\u1ea3n v\u00e0 h\u00e0ng \u0111\u1ea7u th\u1ebf gi\u1edbi) cung c\u1ea5p c\u00e1c d\u1ecbch v\u1ee5 li\u00ean quan \u0111\u1ebfn l\u0129nh v\u1ef1c t\u00ean mi\u1ec1n, hosting, VPS, SSL, email server, v\u00e0 thi\u1ebft k\u1ebf website.\",\"vi\":\"Tenten.vn (\u0111\u1ea1i l\u00fd duy nh\u1ea5t t\u1ea1i Vi\u1ec7t Nam c\u1ee7a Onamae.com-GMO Internet Inc, nh\u00e0 \u0111\u0103ng k\u00fd t\u00ean mi\u1ec1n s\u1ed1 1 Nh\u1eadt B\u1ea3n v\u00e0 h\u00e0ng \u0111\u1ea7u th\u1ebf gi\u1edbi) cung c\u1ea5p c\u00e1c d\u1ecbch v\u1ee5 li\u00ean quan \u0111\u1ebfn l\u0129nh v\u1ef1c t\u00ean mi\u1ec1n, hosting, VPS, SSL, email server, v\u00e0 thi\u1ebft k\u1ebf website.\",\"cn\":\"Tenten.vn (\u0111\u1ea1i l\u00fd duy nh\u1ea5t t\u1ea1i Vi\u1ec7t Nam c\u1ee7a Onamae.com-GMO Internet Inc, nh\u00e0 \u0111\u0103ng k\u00fd t\u00ean mi\u1ec1n s\u1ed1 1 Nh\u1eadt B\u1ea3n v\u00e0 h\u00e0ng \u0111\u1ea7u th\u1ebf gi\u1edbi) cung c\u1ea5p c\u00e1c d\u1ecbch v\u1ee5 li\u00ean quan \u0111\u1ebfn l\u0129nh v\u1ef1c t\u00ean mi\u1ec1n, hosting, VPS, SSL, email server, v\u00e0 thi\u1ebft k\u1ebf website.\"}",
                        "link_": null,
                        "image": "{\"en\":\"\\/upload\\/images\\/A\\/B\\/C\\/D\\/photo_2023-03-03_16-06-57.jpg\",\"vi\":\"{&amp;quot;en&amp;quot;:&amp;quot;\\\\/upload\\\\/images\\\\/image_2023-03-03_14-47-14.png&amp;quot;,&amp;quot;vi&amp;quot;:null,&amp;quot;cn&amp;quot;:null}\",\"cn\":\"{&amp;quot;en&amp;quot;:&amp;quot;\\\\/upload\\\\/images\\\\/image_2023-03-03_14-47-14.png&amp;quot;,&amp;quot;vi&amp;quot;:null,&amp;quot;cn&amp;quot;:null}\"}",
                        "list_task": "{\"en\":[null],\"vi\":[null],\"cn\":[null]}",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 80000000,
                        "code": null,
                        "price": 2350000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 16:10:07",
                        "category": 6,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 127,
                        "name": "HARAVAN",
                        "description": "Haravan với các giải pháp bán hàng đa kênh giúp bạn dễ dàng kinh doanh online và tăng trưởng hiệu quả, dùng thử 14 ngày miễn phí.\nBán hàng đa kênh hiệu quả nhất cho người mới bắt đầu\nChỉ với 200K/Tháng, bạn sẽ nhận được đầy đủ các công cụ\n1 Trang bán hàng tinh gọn, dễ chốt đơn\nBộ phần mềm quản lý & bán hàng trên đa kênh (Social và sàn TMĐT)\nPhần mềm hỗ trợ quản lý bán hàng tại cửa hàng POS\nPhần mềm Chatbot Marketing chốt đơn tự động\nỨng dụng quản lý bán hàng trên điện thoại tiện lợi\nCùng 50+ tiện ích hỗ trợ khác,...",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Haravan với các giải pháp bán hàng đa kênh giúp bạn dễ dàng kinh doanh online và tăng trưởng hiệu quả, dùng thử 14 ngày miễn phí.\nBán hàng đa kênh hiệu quả nhất cho người mới bắt đầu\nChỉ với 200K/Tháng, bạn sẽ nhận được đầy đủ các công cụ\n1 Trang bán hàng tinh gọn, dễ chốt đơn\nBộ phần mềm quản lý & bán hàng trên đa kênh (Social và sàn TMĐT)\nPhần mềm hỗ trợ quản lý bán hàng tại cửa hàng POS\nPhần mềm Chatbot Marketing chốt đơn tự động\nỨng dụng quản lý bán hàng trên điện thoại tiện lợi\nCùng 50+ tiện ích hỗ trợ khác,...",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 20 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 10 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 100000000,
                        "code": null,
                        "price": 3350000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 6,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 128,
                        "name": "ZCOM - Dịch vụ Hosting hàng đầu châu Á",
                        "description": "Z.com – Thương hiệu toàn cầu của tập đoàn GMO Internet. Nhà cung cấp tên miền, hosting hàng đầu châu Á với thành tích hơn 12 triệu tên miền và 750,000 Hosting được đăng ký. Chúng tôi luôn đảm bảo chất lượng dịch vụ hàng đầu, đáng tin cậy với giá cả cạnh tranh.\n\nSản phẩm: Web Hosting",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Z.com – Thương hiệu toàn cầu của tập đoàn GMO Internet. Nhà cung cấp tên miền, hosting hàng đầu châu Á với thành tích hơn 12 triệu tên miền và 750,000 Hosting được đăng ký. Chúng tôi luôn đảm bảo chất lượng dịch vụ hàng đầu, đáng tin cậy với giá cả cạnh tranh.\n\nSản phẩm: Web Hosting",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 15 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 8 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 50000000,
                        "code": null,
                        "price": 1680000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 6,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 129,
                        "name": "Giới thiệu KOC (dành cho publisher)",
                        "description": "KOC là những người có kênh Social với lượng theo dõi lớn hơn 2000 ở các kênh Social bao gồm: Facebook, Youtube, Instagram, TikTok.\nPublisher có nhiệm vụ giới thiệu đối tác KOC mới (New KOC) tải app KOC và tạo ra hoa hồng phát sinh (Occured Commission). Khi đó, bạn sẽ nhận được mức hoa hồng tương ứng.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "KOC là những người có kênh Social với lượng theo dõi lớn hơn 2000 ở các kênh Social bao gồm: Facebook, Youtube, Instagram, TikTok.\nPublisher có nhiệm vụ giới thiệu đối tác KOC mới (New KOC) tải app KOC và tạo ra hoa hồng phát sinh (Occured Commission). Khi đó, bạn sẽ nhận được mức hoa hồng tương ứng.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 15 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 6 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 30000000,
                        "code": null,
                        "price": 1000000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 6,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 130,
                        "name": "Grab doanh nghiệp - thúc đẩy Đông Nam Á phát triển",
                        "description": "Grab là công ty đa dịch vụ hàng đầu Đông Nam Á. Chúng tôi cung cấp nhiều dịch vụ thiết yếu hàng ngày cho hơn 670 triệu người trên khắp Singapore, Indonesia, Malaysia, Thái Lan, Philippines, Việt Nam, Campuchia và Myanmar. Các dịch vụ thiết yếu của chúng tôi bao gồm Giao hàng (giao đồ ăn, giao tạp hóa, giao hàng), Di chuyển (4 bánh, 2 bánh), Dịch vụ Tài chính (cho vay, bảo hiểm, thanh toán không tiền mặt), Doanh nghiệp và các Dịch vụ khác. Sứ mệnh của chúng tôi là thúc đẩy Đông Nam Á phát triển thông qua nỗ lực trao quyền kinh tế cho tất cả mọi người trong khu vực.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Grab là công ty đa dịch vụ hàng đầu Đông Nam Á. Chúng tôi cung cấp nhiều dịch vụ thiết yếu hàng ngày cho hơn 670 triệu người trên khắp Singapore, Indonesia, Malaysia, Thái Lan, Philippines, Việt Nam, Campuchia và Myanmar. Các dịch vụ thiết yếu của chúng tôi bao gồm Giao hàng (giao đồ ăn, giao tạp hóa, giao hàng), Di chuyển (4 bánh, 2 bánh), Dịch vụ Tài chính (cho vay, bảo hiểm, thanh toán không tiền mặt), Doanh nghiệp và các Dịch vụ khác. Sứ mệnh của chúng tôi là thúc đẩy Đông Nam Á phát triển thông qua nỗ lực trao quyền kinh tế cho tất cả mọi người trong khu vực.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK, ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 15 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 8 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 50000000,
                        "code": null,
                        "price": 1680000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 6,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 131,
                        "name": "ABcare – Sản phẩm chăm sóc sức khỏe hàng đầu Châu Âu",
                        "description": "ABcare là nền tảng bán hàng chuyên cung cấp các sản phẩm chăm sóc sức khỏe cá nhân\n\nHiện tại ABcare đang cung cấp và phân phối các sản phẩm đến từ thương hiệu Abena như: Nước rửa vệ sinh phụ nữ, Kem hăm đa năng, Gel lạnh xoa bóp giảm đau. Ngoài ra còn có 1 số nhãn hàng khác như Joydrops, Acon, Biocheck…….\n\nSản phẩm của Abena được bán trên toàn thế giới thông qua các công ty con của mình hoặc qua mạng lưới phân phối độc quyền chuyên biệt. Đây là một phần trong khái niệm tổng cung – khái niệm cung cấp  giải pháp cá nhân, ưu tiên đến chất lượng cuộc sống hàng đầu cho người sử dụng",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "ABcare là nền tảng bán hàng chuyên cung cấp các sản phẩm chăm sóc sức khỏe cá nhân\n\nHiện tại ABcare đang cung cấp và phân phối các sản phẩm đến từ thương hiệu Abena như: Nước rửa vệ sinh phụ nữ, Kem hăm đa năng, Gel lạnh xoa bóp giảm đau. Ngoài ra còn có 1 số nhãn hàng khác như Joydrops, Acon, Biocheck…….\n\nSản phẩm của Abena được bán trên toàn thế giới thông qua các công ty con của mình hoặc qua mạng lưới phân phối độc quyền chuyên biệt. Đây là một phần trong khái niệm tổng cung – khái niệm cung cấp  giải pháp cá nhân, ưu tiên đến chất lượng cuộc sống hàng đầu cho người sử dụng",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK,ZALO c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 10 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 12 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 5 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 20000000,
                        "code": null,
                        "price": 660000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 8,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 132,
                        "name": "{\"en\":\"CALIFORNIA 2022 - T\u1eacP KH\u00d4NG \u0110\u00c3 HO\u00c0N TI\u1ec0N 100%\",\"vi\":\"CALIFORNIA 2022 - T\u1eacP KH\u00d4NG \u0110\u00c3 HO\u00c0N TI\u1ec0N 100%\",\"cn\":\"CALIFORNIA 2022 - T\u1eacP KH\u00d4NG \u0110\u00c3 HO\u00c0N TI\u1ec0N 100%\"}",
                        "description": "{\"en\":\"<p>California Fitness &amp; Yoga (CFYC) l&agrave; m\u1ed9t c&ocirc;ng ty th\u1ec3 d\u1ee5c th\u1ec3 h&igrave;nh t\u1ea1i Vi\u1ec7t Nam, thu\u1ed9c T\u1eadp \u0111o&agrave;n Fitness &amp; Lifestyle Group v&agrave; c&oacute; tr\u1ee5 s\u1edf ch&iacute;nh t\u1ea1i th&agrave;nh ph\u1ed1 H\u1ed3 Ch&iacute; Minh. C&ocirc;ng ty \u0111\u01b0\u1ee3c th&agrave;nh l\u1eadp b\u1edfi &ocirc;ng Dane Fort v&agrave; Randy Dobson. Sau 13 n\u0103m h&igrave;nh th&agrave;nh v&agrave; ph&aacute;t tri\u1ec3n, California Fitness &amp; Yoga \u0111&atilde; ph&aacute;t tri\u1ec3n v\u1edbi chu\u1ed7i th\u01b0\u01a1ng hi\u1ec7u m\u1edf r\u1ed9ng nh\u01b0 California Centuryon v&agrave; Yoga Plus c&ugrave;ng h\u01a1n 30 c&acirc;u l\u1ea1c b\u1ed9 tr&ecirc;n kh\u1eafp Vi\u1ec7t Nam. Hi\u1ec7n nay, Fitness &amp; Yoga - Cung c\u1ea5p c&aacute;c g&oacute;i t\u1eadp v\u1edbi tr\u1ea3i nghi\u1ec7m c&aacute;c ph&ograve;ng t\u1eadp \u0111\u1eb3ng c\u1ea5p 5* t\u1ea1i CFYC. Trang thi\u1ebft b\u1ecb \u0111\u01b0\u1ee3c \u0111\u1ea7u t\u01b0 hi\u1ec7n \u0111\u1ea1i c&ugrave;ng v\u1edbi c&aacute;c HLV gi&agrave;u kinh nghi\u1ec7m v&agrave; chuy&ecirc;n nghi\u1ec7p.<\\/p>\",\"vi\":\"<p>California Fitness &amp; Yoga (CFYC) l&agrave; m\u1ed9t c&ocirc;ng ty th\u1ec3 d\u1ee5c th\u1ec3 h&igrave;nh t\u1ea1i Vi\u1ec7t Nam, thu\u1ed9c T\u1eadp \u0111o&agrave;n Fitness &amp; Lifestyle Group v&agrave; c&oacute; tr\u1ee5 s\u1edf ch&iacute;nh t\u1ea1i th&agrave;nh ph\u1ed1 H\u1ed3 Ch&iacute; Minh. C&ocirc;ng ty \u0111\u01b0\u1ee3c th&agrave;nh l\u1eadp b\u1edfi &ocirc;ng Dane Fort v&agrave; Randy Dobson. Sau 13 n\u0103m h&igrave;nh th&agrave;nh v&agrave; ph&aacute;t tri\u1ec3n, California Fitness &amp; Yoga \u0111&atilde; ph&aacute;t tri\u1ec3n v\u1edbi chu\u1ed7i th\u01b0\u01a1ng hi\u1ec7u m\u1edf r\u1ed9ng nh\u01b0 California Centuryon v&agrave; Yoga Plus c&ugrave;ng h\u01a1n 30 c&acirc;u l\u1ea1c b\u1ed9 tr&ecirc;n kh\u1eafp Vi\u1ec7t Nam. Hi\u1ec7n nay, Fitness &amp; Yoga - Cung c\u1ea5p c&aacute;c g&oacute;i t\u1eadp v\u1edbi tr\u1ea3i nghi\u1ec7m c&aacute;c ph&ograve;ng t\u1eadp \u0111\u1eb3ng c\u1ea5p 5* t\u1ea1i CFYC. Trang thi\u1ebft b\u1ecb \u0111\u01b0\u1ee3c \u0111\u1ea7u t\u01b0 hi\u1ec7n \u0111\u1ea1i c&ugrave;ng v\u1edbi c&aacute;c HLV gi&agrave;u kinh nghi\u1ec7m v&agrave; chuy&ecirc;n nghi\u1ec7p.<\\/p>\",\"cn\":\"<p>California Fitness &amp; Yoga (CFYC) l&agrave; m\u1ed9t c&ocirc;ng ty th\u1ec3 d\u1ee5c th\u1ec3 h&igrave;nh t\u1ea1i Vi\u1ec7t Nam, thu\u1ed9c T\u1eadp \u0111o&agrave;n Fitness &amp; Lifestyle Group v&agrave; c&oacute; tr\u1ee5 s\u1edf ch&iacute;nh t\u1ea1i th&agrave;nh ph\u1ed1 H\u1ed3 Ch&iacute; Minh. C&ocirc;ng ty \u0111\u01b0\u1ee3c th&agrave;nh l\u1eadp b\u1edfi &ocirc;ng Dane Fort v&agrave; Randy Dobson. Sau 13 n\u0103m h&igrave;nh th&agrave;nh v&agrave; ph&aacute;t tri\u1ec3n, California Fitness &amp; Yoga \u0111&atilde; ph&aacute;t tri\u1ec3n v\u1edbi chu\u1ed7i th\u01b0\u01a1ng hi\u1ec7u m\u1edf r\u1ed9ng nh\u01b0 California Centuryon v&agrave; Yoga Plus c&ugrave;ng h\u01a1n 30 c&acirc;u l\u1ea1c b\u1ed9 tr&ecirc;n kh\u1eafp Vi\u1ec7t Nam. Hi\u1ec7n nay, Fitness &amp; Yoga - Cung c\u1ea5p c&aacute;c g&oacute;i t\u1eadp v\u1edbi tr\u1ea3i nghi\u1ec7m c&aacute;c ph&ograve;ng t\u1eadp \u0111\u1eb3ng c\u1ea5p 5* t\u1ea1i CFYC. Trang thi\u1ebft b\u1ecb \u0111\u01b0\u1ee3c \u0111\u1ea7u t\u01b0 hi\u1ec7n \u0111\u1ea1i c&ugrave;ng v\u1edbi c&aacute;c HLV gi&agrave;u kinh nghi\u1ec7m v&agrave; chuy&ecirc;n nghi\u1ec7p.<\\/p>\"}",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "{\"en\":\"California Fitness &amp; Yoga (CFYC) l\u00e0 m\u1ed9t c\u00f4ng ty th\u1ec3 d\u1ee5c th\u1ec3 h\u00ecnh t\u1ea1i Vi\u1ec7t Nam, thu\u1ed9c T\u1eadp \u0111o\u00e0n Fitness &amp; Lifestyle Group v\u00e0 c\u00f3 tr\u1ee5 s\u1edf ch\u00ednh t\u1ea1i th\u00e0nh ph\u1ed1 H\u1ed3 Ch\u00ed Minh.C\u00f4ng ty \u0111\u01b0\u1ee3c th\u00e0nh l\u1eadp b\u1edfi \u00f4ng Dane Fort v\u00e0 Randy Dobson. Sau 13 n\u0103m h\u00ecnh th\u00e0nh v\u00e0 ph\u00e1t tri\u1ec3n, California Fitness &amp; Yoga \u0111\u00e3 ph\u00e1t tri\u1ec3n v\u1edbi chu\u1ed7i th\u01b0\u01a1ng hi\u1ec7u m\u1edf r\u1ed9ng nh\u01b0 California Centuryon v\u00e0 Yoga Plus c\u00f9ng h\u01a1n 30 c\u00e2u l\u1ea1c b\u1ed9 tr\u00ean kh\u1eafp Vi\u1ec7t Nam.Hi\u1ec7n nay, Fitness &amp; Yoga - Cung c\u1ea5p c\u00e1c g\u00f3i t\u1eadp v\u1edbi tr\u1ea3i nghi\u1ec7m c\u00e1c ph\u00f2ng t\u1eadp \u0111\u1eb3ng c\u1ea5p 5* t\u1ea1i CFYC. Trang thi\u1ebft b\u1ecb \u0111\u01b0\u1ee3c \u0111\u1ea7u t\u01b0 hi\u1ec7n \u0111\u1ea1i c\u00f9ng v\u1edbi c\u00e1c HLV gi\u00e0u kinh nghi\u1ec7m v\u00e0 chuy\u00ean nghi\u1ec7p.\",\"vi\":\"California Fitness &amp; Yoga (CFYC) l\u00e0 m\u1ed9t c\u00f4ng ty th\u1ec3 d\u1ee5c th\u1ec3 h\u00ecnh t\u1ea1i Vi\u1ec7t Nam, thu\u1ed9c T\u1eadp \u0111o\u00e0n Fitness &amp; Lifestyle Group v\u00e0 c\u00f3 tr\u1ee5 s\u1edf ch\u00ednh t\u1ea1i th\u00e0nh ph\u1ed1 H\u1ed3 Ch\u00ed Minh.C\u00f4ng ty \u0111\u01b0\u1ee3c th\u00e0nh l\u1eadp b\u1edfi \u00f4ng Dane Fort v\u00e0 Randy Dobson. Sau 13 n\u0103m h\u00ecnh th\u00e0nh v\u00e0 ph\u00e1t tri\u1ec3n, California Fitness &amp; Yoga \u0111\u00e3 ph\u00e1t tri\u1ec3n v\u1edbi chu\u1ed7i th\u01b0\u01a1ng hi\u1ec7u m\u1edf r\u1ed9ng nh\u01b0 California Centuryon v\u00e0 Yoga Plus c\u00f9ng h\u01a1n 30 c\u00e2u l\u1ea1c b\u1ed9 tr\u00ean kh\u1eafp Vi\u1ec7t Nam.Hi\u1ec7n nay, Fitness &amp; Yoga - Cung c\u1ea5p c\u00e1c g\u00f3i t\u1eadp v\u1edbi tr\u1ea3i nghi\u1ec7m c\u00e1c ph\u00f2ng t\u1eadp \u0111\u1eb3ng c\u1ea5p 5* t\u1ea1i CFYC. Trang thi\u1ebft b\u1ecb \u0111\u01b0\u1ee3c \u0111\u1ea7u t\u01b0 hi\u1ec7n \u0111\u1ea1i c\u00f9ng v\u1edbi c\u00e1c HLV gi\u00e0u kinh nghi\u1ec7m v\u00e0 chuy\u00ean nghi\u1ec7p.\",\"cn\":\"California Fitness &amp; Yoga (CFYC) l\u00e0 m\u1ed9t c\u00f4ng ty th\u1ec3 d\u1ee5c th\u1ec3 h\u00ecnh t\u1ea1i Vi\u1ec7t Nam, thu\u1ed9c T\u1eadp \u0111o\u00e0n Fitness &amp; Lifestyle Group v\u00e0 c\u00f3 tr\u1ee5 s\u1edf ch\u00ednh t\u1ea1i th\u00e0nh ph\u1ed1 H\u1ed3 Ch\u00ed Minh.C\u00f4ng ty \u0111\u01b0\u1ee3c th\u00e0nh l\u1eadp b\u1edfi \u00f4ng Dane Fort v\u00e0 Randy Dobson. Sau 13 n\u0103m h\u00ecnh th\u00e0nh v\u00e0 ph\u00e1t tri\u1ec3n, California Fitness &amp; Yoga \u0111\u00e3 ph\u00e1t tri\u1ec3n v\u1edbi chu\u1ed7i th\u01b0\u01a1ng hi\u1ec7u m\u1edf r\u1ed9ng nh\u01b0 California Centuryon v\u00e0 Yoga Plus c\u00f9ng h\u01a1n 30 c\u00e2u l\u1ea1c b\u1ed9 tr\u00ean kh\u1eafp Vi\u1ec7t Nam.Hi\u1ec7n nay, Fitness &amp; Yoga - Cung c\u1ea5p c\u00e1c g\u00f3i t\u1eadp v\u1edbi tr\u1ea3i nghi\u1ec7m c\u00e1c ph\u00f2ng t\u1eadp \u0111\u1eb3ng c\u1ea5p 5* t\u1ea1i CFYC. Trang thi\u1ebft b\u1ecb \u0111\u01b0\u1ee3c \u0111\u1ea7u t\u01b0 hi\u1ec7n \u0111\u1ea1i c\u00f9ng v\u1edbi c\u00e1c HLV gi\u00e0u kinh nghi\u1ec7m v\u00e0 chuy\u00ean nghi\u1ec7p.\"}",
                        "link_": null,
                        "image": "{\"en\":\"\\/upload\\/files\\/Techcombank_logo.png\",\"vi\":null,\"cn\":null}",
                        "list_task": "{\"en\":[null],\"vi\":[null],\"cn\":[null]}",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 5000000,
                        "code": null,
                        "price": 168000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-06 22:05:50",
                        "category": 8,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 133,
                        "name": "FACEIT VN",
                        "description": "Face It là cửa hàng mỹ phẩm đầu tiên của Beauty Blogger Call Me Duy. Cửa hàng luôn đảm bảo sản phẩm chinh hãng, đa dạng phù hợp cho nhiều tinh trạng da và tư vấn nhiệt tình để đảm bảo khách hàng có trải nghiệm tuyệt vời nhất.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "Face It là cửa hàng mỹ phẩm đầu tiên của Beauty Blogger Call Me Duy. Cửa hàng luôn đảm bảo sản phẩm chinh hãng, đa dạng phù hợp cho nhiều tinh trạng da và tư vấn nhiệt tình để đảm bảo khách hàng có trải nghiệm tuyệt vời nhất.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 8 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 3 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 10000000,
                        "code": null,
                        "price": 330000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 8,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 134,
                        "name": "{\"en\":\"CE THI\u1ebeT B\u1eca CH\u00cdNH H\u00c3NG\",\"vi\":\"CE THI\u1ebeT B\u1eca CH\u00cdNH H\u00c3NG\",\"cn\":\"CE THI\u1ebeT B\u1eca CH\u00cdNH H\u00c3NG\"}",
                        "description": "{\"en\":\"<p>C&ocirc;ng ty c\u1ed5 ph\u1ea7n thi\u1ebft b\u1ecb ch&iacute;nh h&atilde;ng CE t\u1ef1 h&agrave;o l&agrave; \u0111\u01a1n v\u1ecb h&agrave;ng \u0111\u1ea7u t\u1ea1i Vi\u1ec7t Nam cung c\u1ea5p thi\u1ebft b\u1ecb l&agrave;m \u0111\u1eb9p ch&iacute;nh h&atilde;ng. CE Marking l&agrave; t&ecirc;n ch&iacute;nh th\u1ee9c c\u1ee7a CE. M\u1ed9t s\u1ea3n ph\u1ea9m n\u1ebfu \u0111\u1ea1t \u0111\u01b0\u1ee3c ch\u1ee9ng nh\u1eadn CE Marking \u0111\u1ed3ng ngh\u0129a v\u1edbi vi\u1ec7c n&oacute; c&oacute; th\u1ec3 l\u01b0u th&ocirc;ng t\u1ef1 do trong th\u1ecb tr\u01b0\u1eddng Ch&acirc;u &Acirc;u, \u0111\u01b0\u1ee3c ph&aacute;p lu\u1eadt c\u1ee7a Li&ecirc;n minh Ch&acirc;u &Acirc;u EU c&ocirc;ng nh\u1eadn.<\\/p>\",\"vi\":\"<p>C&ocirc;ng ty c\u1ed5 ph\u1ea7n thi\u1ebft b\u1ecb ch&iacute;nh h&atilde;ng CE t\u1ef1 h&agrave;o l&agrave; \u0111\u01a1n v\u1ecb h&agrave;ng \u0111\u1ea7u t\u1ea1i Vi\u1ec7t Nam cung c\u1ea5p thi\u1ebft b\u1ecb l&agrave;m \u0111\u1eb9p ch&iacute;nh h&atilde;ng. CE Marking l&agrave; t&ecirc;n ch&iacute;nh th\u1ee9c c\u1ee7a CE. M\u1ed9t s\u1ea3n ph\u1ea9m n\u1ebfu \u0111\u1ea1t \u0111\u01b0\u1ee3c ch\u1ee9ng nh\u1eadn CE Marking \u0111\u1ed3ng ngh\u0129a v\u1edbi vi\u1ec7c n&oacute; c&oacute; th\u1ec3 l\u01b0u th&ocirc;ng t\u1ef1 do trong th\u1ecb tr\u01b0\u1eddng Ch&acirc;u &Acirc;u, \u0111\u01b0\u1ee3c ph&aacute;p lu\u1eadt c\u1ee7a Li&ecirc;n minh Ch&acirc;u &Acirc;u EU c&ocirc;ng nh\u1eadn.<\\/p>\",\"cn\":\"<p>C&ocirc;ng ty c\u1ed5 ph\u1ea7n thi\u1ebft b\u1ecb ch&iacute;nh h&atilde;ng CE t\u1ef1 h&agrave;o l&agrave; \u0111\u01a1n v\u1ecb h&agrave;ng \u0111\u1ea7u t\u1ea1i Vi\u1ec7t Nam cung c\u1ea5p thi\u1ebft b\u1ecb l&agrave;m \u0111\u1eb9p ch&iacute;nh h&atilde;ng. CE Marking l&agrave; t&ecirc;n ch&iacute;nh th\u1ee9c c\u1ee7a CE. M\u1ed9t s\u1ea3n ph\u1ea9m n\u1ebfu \u0111\u1ea1t \u0111\u01b0\u1ee3c ch\u1ee9ng nh\u1eadn CE Marking \u0111\u1ed3ng ngh\u0129a v\u1edbi vi\u1ec7c n&oacute; c&oacute; th\u1ec3 l\u01b0u th&ocirc;ng t\u1ef1 do trong th\u1ecb tr\u01b0\u1eddng Ch&acirc;u &Acirc;u, \u0111\u01b0\u1ee3c ph&aacute;p lu\u1eadt c\u1ee7a Li&ecirc;n minh Ch&acirc;u &Acirc;u EU c&ocirc;ng nh\u1eadn.<\\/p>\"}",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "{\"en\":null,\"vi\":\"C\u00f4ng ty c\u1ed5 ph\u1ea7n thi\u1ebft b\u1ecb ch\u00ednh h\u00e3ng CE t\u1ef1 h\u00e0o l\u00e0 \u0111\u01a1n v\u1ecb h\u00e0ng \u0111\u1ea7u t\u1ea1i Vi\u1ec7t Nam cung c\u1ea5p thi\u1ebft b\u1ecb l\u00e0m \u0111\u1eb9p ch\u00ednh h\u00e3ng. CE Marking l\u00e0 t\u00ean ch\u00ednh th\u1ee9c c\u1ee7a CE. M\u1ed9t s\u1ea3n ph\u1ea9m n\u1ebfu \u0111\u1ea1t \u0111\u01b0\u1ee3c ch\u1ee9ng nh\u1eadn CE Marking \u0111\u1ed3ng ngh\u0129a v\u1edbi vi\u1ec7c n\u00f3 c\u00f3 th\u1ec3 l\u01b0u th\u00f4ng t\u1ef1 do trong th\u1ecb tr\u01b0\u1eddng Ch\u00e2u \u00c2u, \u0111\u01b0\u1ee3c ph\u00e1p lu\u1eadt c\u1ee7a Li\u00ean minh Ch\u00e2u \u00c2u EU c\u00f4ng nh\u1eadn.\",\"cn\":\"C\u00f4ng ty c\u1ed5 ph\u1ea7n thi\u1ebft b\u1ecb ch\u00ednh h\u00e3ng CE t\u1ef1 h\u00e0o l\u00e0 \u0111\u01a1n v\u1ecb h\u00e0ng \u0111\u1ea7u t\u1ea1i Vi\u1ec7t Nam cung c\u1ea5p thi\u1ebft b\u1ecb l\u00e0m \u0111\u1eb9p ch\u00ednh h\u00e3ng. CE Marking l\u00e0 t\u00ean ch\u00ednh th\u1ee9c c\u1ee7a CE. M\u1ed9t s\u1ea3n ph\u1ea9m n\u1ebfu \u0111\u1ea1t \u0111\u01b0\u1ee3c ch\u1ee9ng nh\u1eadn CE Marking \u0111\u1ed3ng ngh\u0129a v\u1edbi vi\u1ec7c n\u00f3 c\u00f3 th\u1ec3 l\u01b0u th\u00f4ng t\u1ef1 do trong th\u1ecb tr\u01b0\u1eddng Ch\u00e2u \u00c2u, \u0111\u01b0\u1ee3c ph\u00e1p lu\u1eadt c\u1ee7a Li\u00ean minh Ch\u00e2u \u00c2u EU c\u00f4ng nh\u1eadn.\"}",
                        "link_": null,
                        "image": "{\"en\":\"\\/upload\\/images\\/a\\/photo_2023-03-03_19-05-53.jpg\",\"vi\":\"{&amp;amp;quot;en&amp;amp;quot;:&amp;amp;quot;\\\\/upload\\\\/images\\\\/A\\\\/B\\\\/C\\\\/D\\\\/photo_2023-03-03_16-07-24.jpg&amp;amp;quot;,&amp;amp;quot;vi&amp;amp;quot;:null,&amp;amp;quot;cn&amp;amp;quot;:null}\",\"cn\":\"{&amp;amp;quot;en&amp;amp;quot;:&amp;amp;quot;\\\\/upload\\\\/images\\\\/A\\\\/B\\\\/C\\\\/D\\\\/photo_2023-03-03_16-07-24.jpg&amp;amp;quot;,&amp;amp;quot;vi&amp;amp;quot;:null,&amp;amp;quot;cn&amp;amp;quot;:null}\"}",
                        "list_task": "{\"en\":[null],\"vi\":[null],\"cn\":[null]}",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 15000000,
                        "code": null,
                        "price": 500000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 19:22:55",
                        "category": 8,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 135,
                        "name": "Sexylook.vn",
                        "description": "SEXYLOOK - Thương hiệu mỹ phẩm nổi tiếng Đài Loan đang làm mưa làm gió với dòng mặt nạ, đặc biệt là dòng mặt nạ đen SEXYLOOK Tràm Trà. Với công dụng nổi bật:\n\nKháng viêm, giảm mụn, ngăn ngừa khả năng quay trở lại\nLoại bỏ dầu thừa, làm sạch sâu và se khít lỗ chân lông.\nChất mask làm từ cotton không dệt kết hợp đá Obsidian hỗ trợ thải độc cho da hiệu quả.",
                        "reson_cancel": "Không đáp ứng đủ các điều kiện ghi nhận kết quả được tính hoa hồng.\nKhông thực hiện nhiệm vụ 3 ngày liên tiếp, và dưới 25 ngày trong 1 tháng\nDùng tài khoản facebook, zalo không chính chủ( acc clone, tạo mới)\nNếu phát hiện Publisher có hành vi gian lận, tạo nhiều tài khoản, hoặc vi phạm các quy định về chạy quảng cáo thì sẽ bị ngưng quyền chạy chiến dịch ngay lập tức \nPublisher vẫn sẽ được hoàn lại phí ràng buộc hợp đồng ban đầu",
                        "short_content": "SEXYLOOK - Thương hiệu mỹ phẩm nổi tiếng Đài Loan đang làm mưa làm gió với dòng mặt nạ, đặc biệt là dòng mặt nạ đen SEXYLOOK Tràm Trà. Với công dụng nổi bật:\n\nKháng viêm, giảm mụn, ngăn ngừa khả năng quay trở lại\nLoại bỏ dầu thừa, làm sạch sâu và se khít lỗ chân lông.\nChất mask làm từ cotton không dệt kết hợp đá Obsidian hỗ trợ thải độc cho da hiệu quả.",
                        "link_": null,
                        "image": null,
                        "list_task": "[\"\u0110\u0103ng b\u00e0i gi\u1edbi thi\u1ec7u tr\u00ean trang FACEBOOK c\u00e1 nh\u00e2n 3 l\u1ea7n tr\u00ean ng\u00e0y, m\u1ed7i b\u00e0i \u0111\u0103ng c\u00e1ch nhau 3 ti\u1ebfng.\nB\u00ecnh lu\u1eadn 5 l\u1ea7n n\u1ed9i dung gi\u1edbi thi\u1ec7u n\u1ec1n t\u1ea3ng McCann Asia tr\u00ean c\u00e1c b\u00e0i vi\u1ebft trong h\u1ed9i nh\u00f3m vi\u1ec7c l\u00e0m.\nGi\u1edbi thi\u1ec7u t\u1ed1i thi\u1ec3u 8 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd m\u1edbi v\u00e0 li\u00ean k\u1ebft th\u00e0nh c\u00f4ng t\u00e0i kho\u1ea3n ng\u00e2n h\u00e0ng tr\u00ean trang Mccannasia.com\nT\u1ed1i thi\u1ec3u 3 th\u00e0nh vi\u00ean \u0111\u0103ng k\u00fd th\u00e0nh c\u00f4ng chi\u1ebfn l\u01b0\u1ee3c tr\u00ean trang Mccannasia.com\"]",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": 10000000,
                        "code": null,
                        "price": 330000,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-03 14:54:30",
                        "updated_at": "2023-03-03 14:54:59",
                        "category": 8,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 138,
                        "name": "{\"en\":null,\"vi\":null,\"cn\":null}",
                        "description": "{\"en\":null,\"vi\":null,\"cn\":null}",
                        "reson_cancel": null,
                        "short_content": "{\"en\":null,\"vi\":null,\"cn\":null}",
                        "link_": null,
                        "image": "{\"en\":null,\"vi\":null,\"cn\":null}",
                        "list_task": "{\"en\":[null],\"vi\":[null],\"cn\":[null]}",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": null,
                        "code": null,
                        "price": null,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-12 14:01:20",
                        "updated_at": "2023-03-12 14:01:20",
                        "category": null,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 139,
                        "name": "{\"en\":null,\"vi\":null,\"cn\":null}",
                        "description": "{\"en\":null,\"vi\":null,\"cn\":null}",
                        "reson_cancel": null,
                        "short_content": "{\"en\":null,\"vi\":null,\"cn\":null}",
                        "link_": null,
                        "image": "{\"en\":null,\"vi\":null,\"cn\":null}",
                        "list_task": "{\"en\":[null],\"vi\":[null],\"cn\":[null]}",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": null,
                        "code": null,
                        "price": null,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-12 14:09:58",
                        "updated_at": "2023-03-12 14:09:58",
                        "category": null,
                        "mission_id": null,
                        "is_hot": 0,
                        "is_beginner": 0,
                        "campain_category": null
                    },
                    {
                        "id": 140,
                        "name": "{\"en\":\"1222111\",\"vi\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"cn\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\"}",
                        "description": "{\"en\":\"<p>{&quot;en&quot;:null,&quot;vi&quot;:null,&quot;cn&quot;:null}<\\/p>\",\"vi\":\"<p>{&quot;en&quot;:null,&quot;vi&quot;:null,&quot;cn&quot;:null}<\\/p>\",\"cn\":\"<p>{&quot;en&quot;:null,&quot;vi&quot;:null,&quot;cn&quot;:null}<\\/p>\"}",
                        "reson_cancel": null,
                        "short_content": "{\"en\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"vi\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"cn\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\"}",
                        "link_": null,
                        "image": "{\"en\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"vi\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"cn\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\"}",
                        "list_task": "{\"en\":[null],\"vi\":[null],\"cn\":[null]}",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": null,
                        "code": null,
                        "price": null,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-12 14:14:10",
                        "updated_at": "2023-03-12 14:22:31",
                        "category": null,
                        "mission_id": 2,
                        "is_hot": 0,
                        "is_beginner": 1,
                        "campain_category": "category-2"
                    },
                    {
                        "id": 141,
                        "name": "{\"en\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"vi\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"cn\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\"}",
                        "description": "{\"en\":\"<p>{&quot;en&quot;:null,&quot;vi&quot;:null,&quot;cn&quot;:null}<\\/p>\",\"vi\":\"<p>{&quot;en&quot;:null,&quot;vi&quot;:null,&quot;cn&quot;:null}<\\/p>\",\"cn\":\"<p>{&quot;en&quot;:null,&quot;vi&quot;:null,&quot;cn&quot;:null}<\\/p>\"}",
                        "reson_cancel": null,
                        "short_content": "{\"en\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"vi\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"cn\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\"}",
                        "link_": null,
                        "image": "{\"en\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"vi\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\",\"cn\":\"{&amp;amp;amp;quot;en&amp;amp;amp;quot;:null,&amp;amp;amp;quot;vi&amp;amp;amp;quot;:null,&amp;amp;amp;quot;cn&amp;amp;amp;quot;:null}\"}",
                        "list_task": "{\"en\":[null],\"vi\":[null],\"cn\":[null]}",
                        "date_public": null,
                        "date_end": null,
                        "price_day": null,
                        "registration_fee": null,
                        "code": null,
                        "price": null,
                        "status": null,
                        "users": null,
                        "created_at": "2023-03-12 14:19:03",
                        "updated_at": "2023-03-13 14:21:39",
                        "category": 1,
                        "mission_id": 2,
                        "is_hot": 1,
                        "is_beginner": 1,
                        "campain_category": "category-1"
                    }
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
                (item) =>
                    this.selectedRows.findIndex((row) => row.key === item.key) === -1
            );
            this.selectedRows = [];
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
