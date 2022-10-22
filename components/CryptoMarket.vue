<template>
    <div>
        <DxDataGrid id="dataGrid" :data-source="remoteDataSource" key-expr="id" :show-borders="false"
            :column-auto-width="true" :allowColumnResizing="true" :focused-row-enabled="true" :show-row-lines="false"
            :show-column-lines="false">

            <DxSearchPanel :visible="true" />
            <DxHeaderFilter :visible="true" />

            <DxColumn caption="" alignment="right" :width="40" :allow-sorting="false" data-field="image"
                cell-template="currenctSymbolTemplate" :allow-header-filtering="false" />
            <template #currenctSymbolTemplate="{ data }">
                <img :src="data.value">
            </template>

            <DxColumn caption="Cryptocurrency Name" :width="170" :allow-header-filtering="false" :allow-sorting="true"
                :calculate-cell-value="calculateCellValue" />

            <DxColumn caption="Price USD" data-field="current_price" format="decimal" cell-template="priceTemplate">
                <DxHeaderFilter :data-source="priceHeaderFilter" />
            </DxColumn>
            <template #priceTemplate="{ data }">
                <p>${{data.value}}</p>
            </template>

            <DxColumn caption="Market Cap" data-field="market_cap" format="currency">
                <DxHeaderFilter :data-source="marketCapHeaderFilter" />
            </DxColumn>
            <DxColumn caption="Total Volume" data-field="total_volume" format="currency">
                <DxHeaderFilter :data-source="marketCapHeaderFilter" />
            </DxColumn>

            <DxColumn caption="% Change 24h" data-field="price_change_percentage_24h" :format="priceChangeFormat">
                <DxHeaderFilter :data-source="priceChangeHeaderFilter" />
            </DxColumn>

            <DxSorting mode="multiple" />

            <DxPaging :page-size="50" />
            <DxPager :visible="true" :show-navigation-buttons="true" />
        </DxDataGrid>
    </div>
</template>

<script >
import 'devextreme/dist/css/dx.light.css';
import {
    DxDataGrid,
    DxRemoteOperations,
    DxHeaderFilter,
    DxSorting,
    DxPager,
    DxColumn,
    DxSearchPanel,
    DxPaging,
    DxColumnHeaderFilter
} from 'devextreme-vue/data-grid';
import { createStore } from 'devextreme-aspnet-data-nojquery';

const serviceUrl = 'https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=250&page=1&sparkline=false';
const remoteDataSource = createStore({
    key: 'id',
    loadUrl: serviceUrl
});

export default {
    components: {
        DxDataGrid,
        DxRemoteOperations,
        DxSorting,
        DxPager,
        DxColumn,
        DxSearchPanel,
        DxPaging,
        DxHeaderFilter,
        DxColumnHeaderFilter
    },
    data() {
        return {
            remoteDataSource: remoteDataSource,

            priceChangeFormat: {
                type: "fixedPoint",
                precision: 2
            },

            priceHeaderFilter: [
                {
                    text: 'Less than $0.001',
                    value: ['current_price', '<', 0.001]
                },
                {
                    text: '$0.001 - $0.01',
                    value: [
                        ['current_price', '>=', 0.001],
                        ['current_price', '<', 0.01]
                    ]
                },
                {
                    text: '$0.01 - $0.1',
                    value: [
                        ['current_price', '>=', 0.01],
                        ['current_price', '<', 0.1]
                    ]
                },
                {
                    text: '$0.1 - $1',
                    value: [
                        ['current_price', '>=', 0.1],
                        ['current_price', '<', 1]
                    ]
                },
                {
                    text: '$1 - $10',
                    value: [
                        ['current_price', '>=', 1],
                        ['current_price', '<', 10]
                    ]
                },
                {
                    text: '$10 - $100',
                    value: [
                        ['current_price', '>=', 10],
                        ['current_price', '<', 100]
                    ]
                },
                {
                    text: '$100 - $1000',
                    value: [
                        ['current_price', '>=', 100],
                        ['current_price', '<', 1000]
                    ]
                },
                {
                    text: 'More than $1000',
                    value: ['current_price', '>', 1000]
                }],
            marketCapHeaderFilter: [
                {
                    text: 'Less than $100M',
                    value: ['market_cap', '<', 100000000]
                },
                {
                    text: '$100M - $1B',
                    value: [
                        ['market_cap', '>=', 100000000],
                        ['market_cap', '<', 1000000000]
                    ]
                },
                {
                    text: '$1B - $10B',
                    value: [
                        ['market_cap', '>=', 1000000000],
                        ['market_cap', '<', 10000000000]
                    ]
                },
                {
                    text: '$10B - $100B',
                    value: [
                        ['market_cap', '>=', 10000000000],
                        ['market_cap', '<', 100000000000]
                    ]
                },
                {
                    text: 'More than $100B',
                    value: ['market_cap', '>=', 100000000000]
                }],
            priceChangeHeaderFilter: [{
                text: 'Less than 0 (loser)',
                value: ['price_change_percentage_24h', '<', 0],
            }, {
                text: 'more than 0 (gainer)',
                value: [
                    ['price_change_percentage_24h', '>', 0]
                ],
            }],
        }
    },
    methods: {
        calculateCellValue(data) {
            return [data.symbol.toUpperCase(), data.name].join(' ');
        }
    }
}
</script>

<style>
#dataGrid {
    width: 75%;
    margin: auto;
}

img {
    height: 25px;
    display: block;
}
</style>
