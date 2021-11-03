<template>
    <div class="map">
        <h3>Карта офиса</h3>

        <div
            v-if="!isLoading"
            class="map-root"
        >
            <MapSVG ref="map" />
            <TableSVG
                v-show="false"
                ref="table"
            />
        </div>
        <div v-else>Loading...</div>
    </div>
</template>

<script>
import MapSVG from '@/assets/images/map.svg';
import TableSVG from '@/assets/images/workPlace.svg';
import * as d3 from 'd3';
import tables from "@/assets/data/tables.json";
import legend from "@/assets/data/legend.json";

export default {
    components: {
        MapSVG,
        TableSVG,
    },
    data() {
        return {
            isLoading: false,
            mapSvg: null,
            tableSvg: null,
            tables: [],
        };
    },
    created() {
        this.tables = tables;
    },
    mounted() {
        this.isLoading = true;

        this.mapSvg = d3.select(this.$refs.map).select("g");
        this.tableSvg = d3.select(this.$refs.table);

        if (this.mapSvg) {
            this.drawTables();
        } else {
            console.log("SVG is incorrect");
        };
        
        this.isLoading = false;
    },
    methods: {
        drawTables() {
            const svgTablesGroupPlace = this.mapSvg
                .append("g")
                .classed("groupPlaces", true);
            this.tables.map((table) => {
                svgTablesGroupPlace
                    .append("g")
                    .classed("employer-place", true)
                    .attr("transform", `translate(${table.x}, ${table.y}) scale(0.5) rotate(${table.rotate || 0})`) 
                    .attr("id", table._id)
                    .attr("group_id", table.group_id)
                    .html(this.tableSvg.html())
                    .attr(
                        "fill",
                        legend.find((it) => it.group_id === table.group_id) 
                            ?.color ?? "transparent"
                    );
            });
        },
    },
};
</script>

<style scoped>
.map {
    height: 100%;
    width: 100%;
    padding: 24px;
    overflow: hidden;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
}

.map-root {
    height: 100%;
    width: 100%;
    overflow: hidden;
    box-sizing: border-box;
}

h3 {
    margin-top: 0px;
}

::v-deep svg {
    height: 100%;
    width: 100%;
}

::v-deep .table {
    cursor: pointer;
}
</style>
