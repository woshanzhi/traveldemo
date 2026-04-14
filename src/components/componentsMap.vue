<template>
<div id="index" ref="appRef">
    <div id="container" ref="mapRef"></div>
</div>
</template>

<script>
var map;
var districtExplorer;
var currentAreaNode = null;

export default {
    name: "componentsMap",
    data() {
        return {
            adcode: 100000,
        };
    },
    mounted() {
        this.$nextTick(() => this.initMap());
    },
    beforeDestroy() {
        map?.destroy();
        map = null;
    },
    methods: {
        initMap() {
            map = new window.AMap.Map("container", {
                terrain: false,
                showBuildingBlock: false,
                showLabel: false,
                scrollWheel: true,
                dragEnable: true,
                doubleClickZoom: false,
            });
            this.setMapUI();
        },
        setMapUI() {
            window.AMapUI.load(["ui/geo/DistrictExplorer", "lib/$"], (DistrictExplorer, $) => {
                districtExplorer = new DistrictExplorer({
                    eventSupport: true,
                    map: map,
                });
                this.switch2AreaNode(this.adcode);

                const $tipMarkerContent = $('<div class="tipMarker top"></div>');
                const tipMarker = new window.AMap.Marker({
                    content: $tipMarkerContent.get(0),
                    offset: new window.AMap.Pixel(0, 0),
                    bubble: true,
                });

                function toggleHoverFeature(feature, isHover, position) {
                    tipMarker.setMap(isHover ? map : null);
                    if (!feature) return;

                    const props = feature.properties;
                    if (isHover) {
                        $tipMarkerContent.html(`${props.adcode}: ${props.name}`);
                        tipMarker.setPosition(position || props.center);
                    }
                }

                districtExplorer.on("featureMouseout featureMouseover", (e, feature) => {
                    toggleHoverFeature(feature, e.type === "featureMouseover", e.originalEvent?.lnglat);
                });

                districtExplorer.on("featureClick", (e, feature) => {
                    const props = feature.properties;
                    if (props.childrenNum > 0) {
                        this.switch2AreaNode(props.adcode);
                    }
                });

                districtExplorer.on("outsideClick", (e) => {
                    districtExplorer.locatePosition(e.originalEvent.lnglat, (error, routeFeatures) => {
                        if (routeFeatures && routeFeatures.length > 1) {
                            this.switch2AreaNode(routeFeatures[1].properties.adcode);
                        } else {
                            this.switch2AreaNode(100000);
                        }
                    }, { levelLimit: 2 });
                });
            });
        },
        renderAreaPolygons(areaNode) {
            const colors = ["#3366cc", "#dc3912", "#ff9900", "#109618", "#990099"];
            map.setBounds(areaNode.getBounds(), null, null, true);
            districtExplorer.clearFeaturePolygons();

            districtExplorer.renderSubFeatures(areaNode, (feature, i) => {
                const fillColor = colors[i % colors.length];
                return {
                    cursor: "default",
                    bubble: true,
                    strokeColor: "black",
                    strokeOpacity: 1,
                    strokeWeight: 1,
                    fillColor: fillColor,
                    fillOpacity: 0.35,
                };
            });

            districtExplorer.renderParentFeature(areaNode, {
                cursor: "default",
                bubble: true,
                strokeColor: "black",
                strokeOpacity: 1,
                strokeWeight: 1,
                fillColor: colors[0],
                fillOpacity: 0.35,
            });

            this.drawHeatMap();
        },
        drawHeatMap() {
            const heatmap = new window.AMap.Heatmap(map, {
                radius: 30,
                opacity: [0, 0.8],
            });

            heatmap.setDataSet({
                data: [
                    { lng: 116.397128, lat: 39.916527, value: 100 },
                    { lng: 116.405285, lat: 39.904989, value: 90 },
                ],
                max: 100,
            });
        },
        switch2AreaNode(adcode, callback) {
            if (currentAreaNode && `${currentAreaNode.getAdcode()}` === `${adcode}`) return;
            this.loadAreaNode(adcode, (error, areaNode) => {
                if (error) return;
                currentAreaNode = areaNode;
                districtExplorer.setAreaNodesForLocating([currentAreaNode]);
                districtExplorer.setHoverFeature(null);
                this.renderAreaPolygons(areaNode);
                if (callback) callback(null, areaNode);
            });
        },
        loadAreaNode(adcode, callback) {
            districtExplorer.loadAreaNode(adcode, (error, areaNode) => {
                if (error) return callback(error);
                callback(null, areaNode);
            });
        },
    },
};
</script>

<style lang="scss" scoped>
#index {
    width: 100vw;
    height: 100vh;
    color: #ffffff;
    overflow: hidden;
    #container {
        width: 100vw;
        height: 100vh;
        z-index: 100;
    }
}
::v-deep .tipMarker {
    color: #555;
    background-color: rgba(255, 254, 239, 0.8);
    border: 1px solid #7e7e7e;
    padding: 2px 6px;
    font-size: 12px;
    white-space: nowrap;
    display: inline-block;
}
.tipMarker:before,
.tipMarker:after {
    content: "";
    display: block;
    position: absolute;
    margin: auto;
    width: 0;
    height: 0;
    border: solid transparent;
    border-width: 5px 5px;
}
.tipMarker.top {
    transform: translate(-50%, -110%);
}
.tipMarker.top:before,
.tipMarker.top:after {
    bottom: -9px;
    left: 0;
    right: 0;
    border-top-color: rgba(255, 254, 239, 0.8);
}
.tipMarker.top:before {
    bottom: -10px;
    border-top-color: #7e7e7e;
}
</style>
