<template>
    <div id="map"></div>
    <Title title="大足公交206路" />
    <Time :time="time" />
    <div style="position: absolute; width: 75px; height: 30px; bottom: 10px; right: 10px; background-color: rgba(81, 133, 247, 0.95); color: #fff; border-radius: 5px; border: 2px solid #fff; display: flex; align-items: center; justify-content: center; font-weight: bold; cursor: pointer; z-index: 100;" @click="getBusData()">
        手动刷新
    </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'
import mapboxgl from 'mapbox-gl'
import 'mapbox-gl/dist/mapbox-gl.css'
import './css/framework.css'

import axios from 'axios'

import Title from './components/Title.vue'
import Time from './components/Time.vue'

import _ from './resource/bus.json'
import stations2061 from './resource/206Stations1.json'
import stations2062 from './resource/206Stations2.json'
// import currentBus from './resource/temp/currentBus.json'

let busGeojson: any = _;

(() => {
    // busGeojson.geometry.coordinates.map((item: any, index: number) => {
    //     busGeojson.geometry.coordinates[index][0] = Number(item[0]) - 0.003674339142245276
    //     busGeojson.geometry.coordinates[index][1] = Number(item[1]) + 0.0027023766465736776
    // })

    // stations206.data.siteDtos.map((item: any, index: number) => {
    //     stations206.data.siteDtos[index].longitude = String(Number(item.longitude) - 0.003674339142245276)
    //     stations206.data.siteDtos[index].latitude = String(Number(item.latitude) + 0.0027023766465736776)
    // })

    // console.log(JSON.stringify(stations206))
})()

let map: mapboxgl.Map, popup: mapboxgl.Popup
mapboxgl.accessToken = 'pk.eyJ1IjoieW93b3QiLCJhIjoiY2xmbmUxYnl2MGwzdjN5bXYzdnB2aGhjYyJ9.wDYgg0msNnU7ZetZLcJzUQ'

let time = ref('' as string)

onMounted(() => {
    popup = new mapboxgl.Popup({
        closeButton: false,
        closeOnClick: false
    })

    map = new mapboxgl.Map({
        container: 'map',
        attributionControl: false,
        style: 'mapbox://styles/yowot/clb6u8dyf002714nxscjtgrya',
        center: [107.79942839007867, 37.093496518166944],
        zoom: 2,
    })
    map.setProjection('mercator')

    map.on('load', () => {
        map.addSource('bus', {
            type: 'geojson',
            data: busGeojson
        })
        map.addLayer({
            id: 'bus',
            type: 'line',
            source: 'bus',
            paint: {
                'line-color': '#3D87FF',
                'line-width': 5
            },
        })

        let bounds = new mapboxgl.LngLatBounds()
        busGeojson.geometry.coordinates.forEach((item: any) => {
            bounds.extend([item[0], item[1]])
        })
        map.fitBounds(bounds, {
            padding: 20,
            animate: false
        })

        stations2061.data.siteDtos.map(item => {
            let station = document.createElement('div')
            station.className = 'station-marker'
            new mapboxgl.Marker(station).setLngLat([Number(item.longitude), Number(item.latitude)]).addTo(map)

            station.onmouseover = () => {
                popup.setLngLat([Number(item.longitude), Number(item.latitude)]).setHTML(`
                    <div><span class="info-bar">站点</span>${item.name}</div>
                `).addTo(map)
            }

            station.onmouseleave = () => {
                popup.remove()
            }
        })

        map.on('click', e => {
            console.log(e.lngLat)
        })
    })

    // currentBus.data[0].list.map(item => {
    //     let bus = document.createElement('div')
    //     let lngLat = item.lngLat.split(',')
    //     lngLat[0] = String(Number(lngLat[0]) - 0.003674339142245276)
    //     lngLat[1] = String(Number(lngLat[1]) + 0.0027023766465736776)
    //     bus.className = 'bus-marker'
    //     new mapboxgl.Marker(bus).setLngLat([Number(lngLat[0]), Number(lngLat[1])]).addTo(map)

    //     bus.onmouseover = () => {
    //         popup.setLngLat([Number(lngLat[0]), Number(lngLat[1])]).setHTML(`
    //             <div>
    //                 <span class="info-bar">车辆</span>${item.plateCode}
    //                 <br>
    //                 下一站: ${stations206.data.siteDtos[item.nextNum - 1].name}
    //             </div>
    //         `).addTo(map)
    //     }

    //     bus.onmouseleave = () => {
    //         popup.remove()
    //     }
    // })
})

let getBusData = () => {
    document.querySelectorAll('.bus-h-marker').forEach(item => {
        item.remove()
    })

    document.querySelectorAll('.bus-g-marker').forEach(item => {
        item.remove()
    })

    let date = new Date()
    let DD = date.getDate() < 10 ? '0' + date.getDate() : date.getDate()
    let hh = date.getHours() < 10 ? '0' + date.getHours() : date.getHours()
    let mm = date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes()
    time.value = `${DD}日 ${hh}时${mm}分`

    axios.get('https://bus.yowot.cn/206/1').then(data => {
        let json = data.data
        json.data[0].list.map((item: any) => {
            let bus = document.createElement('div')
            let lngLat = item.lngLat.split(',')
            lngLat[0] = String(Number(lngLat[0]) - 0.003674339142245276)
            lngLat[1] = String(Number(lngLat[1]) + 0.0027023766465736776)
            bus.className = 'bus-h-marker'
            new mapboxgl.Marker(bus).setLngLat([Number(lngLat[0]), Number(lngLat[1])]).addTo(map)

            bus.onmouseover = () => {
                popup.setLngLat([Number(lngLat[0]), Number(lngLat[1])]).setHTML(`
                    <div>
                        <span class="info-bar">车辆</span>${item.plateCode}
                        <br>
                        下一站: ${stations2061.data.siteDtos[item.nextNum - 1].name}
                        <br>
                        开往: 宏声广场
                    </div>
                `).addTo(map)
            }

            bus.onmouseleave = () => {
                popup.remove()
            }
        })
    })

    axios.get('https://bus.yowot.cn/206/2').then(data => {
        let json = data.data
        json.data[0].list.map((item: any) => {
            let bus = document.createElement('div')
            let lngLat = item.lngLat.split(',')
            lngLat[0] = String(Number(lngLat[0]) - 0.003674339142245276)
            lngLat[1] = String(Number(lngLat[1]) + 0.0027023766465736776)
            bus.className = 'bus-g-marker'
            new mapboxgl.Marker(bus).setLngLat([Number(lngLat[0]), Number(lngLat[1])]).addTo(map)

            bus.onmouseover = () => {
                popup.setLngLat([Number(lngLat[0]), Number(lngLat[1])]).setHTML(`
                    <div>
                        <span class="info-bar">车辆</span>${item.plateCode}
                        <br>
                        下一站: ${stations2062.data.siteDtos[item.nextNum - 1].name}
                        <br>
                        开往: 高铁大足南站
                    </div>
                `).addTo(map)
            }

            bus.onmouseleave = () => {
                popup.remove()
            }
        })
    })
}

getBusData()
</script>