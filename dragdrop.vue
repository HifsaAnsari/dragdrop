<template>
	<div id="vue-root">
		<!-- <loading :active.sync="isLoading"></loading> -->
		<div class="container-fluid vue-template">
			<div class="row">
				<div class="col-md-12">
					<div class="heading_c">
						<span class="icon fleet_dashboard_icon"></span>&nbsp;
						<span>Drag And Drop</span>
					</div>
					<div class="body">
						<!-- Timeline modal  -->
						<div id="modal_container"></div>

						<div class="row" style="margin: 0px">
							<!-- time option selection -->

							<div class="col-md-12">
								<div class="row">
									<!-- Period -->

									<div class="col-md-4">
										<label class="mt-3">
											<b>period</b>
										</label>

										<multiselect name="period" id="period" class="period" style="font-size: 12px"
											v-model="form.startDate_type" select-label="Select"
											deselect-label="Can't Remove" track-by="id" label="title"
											placeholder="Select one" :options="period" @select="dateCalculation"
											@input="griddate()" :searchable="true" :allow-empty="false"
											:max-height="500"></multiselect>
									</div>

									<!--Start Date-->

									<div class="col-md-4">
										<label class="mt-3">
											<b>from</b>
										</label>

										<div id="date_from">
											<date-picker name="startDate" type="dateTime"
												placeholder="Select Date From..." format="DD-MM-YYYY HH:mm:ss"
												@change="griddate()" :lang="lang" confirm :not-after="form.endDate"
												v-model="form.startDate"></date-picker>
										</div>
									</div>

									<!-- end date -->

									<div class="col-md-4">
										<label class="mt-3">
											<b>to</b>
										</label>

										<div id="date_to">
											<date-picker name="endDate" type="dateTime" placeholder="Select Date To..."
												format="DD-MM-YYYY HH:mm:ss" @change="griddate()" :lang="lang" confirm
												:not-before="form.startDate" v-model="form.endDate"></date-picker>
										</div>
									</div>

									<!-- end date -->
								</div>
							</div>
							<!--  option row end -->
						</div>

						<!-- drag drop section -->
						<div class="row" style="margin: 0px">
							<div class="col-md-12">
								<div class="dragdrop">
									<div class="gridwrapper">
										<!--  Dates  -->

										<div class="datebox">
											<div class="emptydiv"></div>
											<div class="date" v-for="(date, index1) in nextWeekDates" :key="index1">
												{{ date }}
											</div>
										</div>

										<div class="gridcontainer">
											<!-- Sites -->

											<div class="outergrid" v-for="(sites, index2) in noofsites" :key="index2">

												<!-- <div class="sitesbox">{{ sites.title }}</div> -->
												<div class="sitesbox">{{ sites.name }}</div>



												<!-- whitebox -->
												<div class="dragdata">
													<div :id="'sortable2' + [index3]" class="innergrid"
														v-for="(inner, index3) in nextWeekDates.length" :key="index3">
														<!-- dropbox  value store in empty box drop -->
														<div id="sortable2">
															
															<ul :id="[index2 + '-' + index3]" class="dropfalse target">

															</ul>
														</div>

														<!-- Button to invoke the modal -->

														<button type="button"
															class="btn btn-sm btn-primary btn_modal_detail"
															data-toggle="modal"
															:id="'buttonid_' + [index2 + '-' + index3]"
															data-open="my_Modal" @click="Showmodal">
															Detail
														</button>
													</div>
												</div>
											</div>
										</div>
									</div>
								</div>
								<div class="save_button_div">
									<button ype="button" class="btn btn-sm btn-primary Save_Change_btn" id=""
										@click="SaveChanges">
										Save Change
									</button>
									<div id="output"></div>
									<button ype="button" class="btn btn-sm btn-primary Save_Change_btn" id=""
										@click="SaveData_view">
									get data
									</button>
								</div>

								<!-- col 12 end -->
							</div>
							<!-- dragdrop row end -->
						</div>

						<!-- Queue box -->
						<div class="row" style="margin: 0px">
							<div class="col-md-12">
								<div class="queuemaincontainer">
									<div class="queueC_ontainer_">
										<h1 class="heading_jobQueue">Job Queue</h1>

										<div class="scrumboard">
											<div class="QueueList">
												<ul class="queueBox droptrue" id="sortable1"
													v-for="(element, index7) in arrBackLog" :key="index7">
													<li class="portlet ui-state-default list_arr">
														<span id="officer_index" style="display: none">{{
																index7
														}}</span>
														<span class="portlet-header" id="officer_name">{{
																element.name
														}}</span>
														<span class="portlet-content officer_date"
															id="officer_date">{{ element.start }}
															{{ element.end }}</span>
													</li>
												</ul>
											</div>
										</div>
									</div>
								</div>
							</div>
							<!--row Queue box end-->
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
// import Loading from "vue-loading-overlay";
import DatePicker from "vue2-datepicker";
import { v4 as uuidv4 } from "uuid";
import Multiselect from "vue-multiselect";
import "vue-loading-overlay/dist/vue-loading.css";
require("vue-multiselect/dist/vue-multiselect.min.css");
Vue.component("multiselect", Multiselect);
var currentdate = new Date();

export default {
	name: "Drag_Drop",
	props: ["pre_data"],
	components: {
		// components: { Loading },
	},
	data() {
		return {
			buttonid_: uuidv4(),
			// isLoading: false,
			isSubmitted: false,
			generateid: null,
			noofsites: [
				{ name: "sites1" },
				{ name: "sites2" },
				{ name: "sites3" },
				{ name: "sites4" },
			],
			//  noofsites: this.pre_data.sites,
			nextWeekDates: [],
			newTask: "",
			current_date:
				currentdate.getFullYear() +
				"-" +
				(currentdate.getMonth() + 1) +
				"-" +
				currentdate.getDate(),
			noofdays: 1,
			newTask: "",
			timeline: [],
			Save_modal_Data: [],
			modalid: "",
			// arrBackLog:this.pre_data.officers,
			// arrBackLog: [
			// 	{
			// 		name: this.pre_data.officers[0].name,
			// 		id: 1,
			// 		date: "2022-04-8",
			// 		start: "4:00:00",
			// 		end: "15:00:00",
			// 	},
			// ],

			arrBackLog: [
				{
					name: "Aqsa khan",
					jobid: 1,
					date: "2022-04-8",
					start: "4:00:00",
					end: "15:00:00",
				},
				{
					name: "Sheeba Ali",
					jobid: 2,
					date: "2022-04-8",
					start: "9:00:00",
					end: "14:00",
				},
				{
					name: "sara",
					jobid: 3,
					date: "2022-04-8",
					start: "9:00:00",
					end: "14:00:00",
				},
				{
					name: "Hifsa Ansari",
					Jobid: 4,
					date: "2022-04-8",
					start: "9:00:00",
					end: "18:00:00",
				},
			],

			form: {
				startDate: null,
				endDate: null,
				startDate_type: { id: "nextweek", title: "Next week" },
				custom: false,
			},
			period: [
				{ id: "custom", title: "User Defined" },
				{ id: "nextweek", title: "Next week" },
				{ id: "today", title: "Today" },
				{ id: "yesterday", title: "Yesterday" },
				{ id: "this_week", title: "This Week" },
				{ id: "last_week", title: "Last Week" },
				{ id: "this_month", title: "This Month" },
				{ id: "last_month", title: "Last Month" },
			],
			lang: "en",
			formula: [],
			_diff_time: [],
			_duration_job: [],
			Jst: [],
			je: [],



		};
	},
	mounted() {
		this.millisToMinutesAndSeconds();

		var that = this;
		// timeline print
		$(function () {
			for (var i = 0; i < 24; i++) {
				console.log(i);
				if (i < 10) {
					i = "0" + i;
					console.log("0" + i);
				}

				for (var j = 0; j < 60; j = j + 30) {
					var midday = i < 12 ? "am" : "pm";
					var storetime = i + ":" + j + "" + midday;
					that.timeline.push(storetime);
					// console.log(that.timeline)
				}
			}
			// for modal runtime
			that.storeArry();
		});
		// period(input option) ka function
		this.dateCalculation(this.form.startDate_type);
		// grid ka function hy
		this.nextweek();
		// aboove option ka function jab hum select karte hai
		this.griddate();

		// drag and drop function
		$(function () {
			$(".portlet")
				.addClass(
					"ui-widget ui-widget-content ui-helper-clearfix ui-corner-all"
				)
				.find(".portlet-header")
				.addClass("ui-widget-header ui-corner-all officername");
			$(".portlet-toggle").click(function () {
				var icon = $(this);
				icon.toggleClass("ui-icon-minusthick ui-icon-plusthick");
				icon.closest(".portlet").find(".portlet-content").toggle();
			});

			//dragbox jo hai wo (Queue box)hy
			$(" ul.droptrue").sortable({
				connectWith: "ul",
				revert: true,
				handle: ".portlet-header",
				cancel: ".portlet-toggle",
				placeholder: "portlet-placeholder ui-corner-all",

			});

			// dropbox ( grid ) hai jis me hum drop kar rahe hai queuebox se leekar
			$("ul.dropfalse")
				.sortable({
					connectWith: "ul",
					revert: true,
					cancel: ".portlet-toggle",
					receive: function (event, ui, locations) {
						ui.item.attr("draggable", "false");
						$("#sortable1 li").each(function (index7) {
							 that.formula.push($(this).html());
							 console.log(that.formula,"	 that.formula")
						});
						// id picker
						console.log($(this))  // ul#2-0. (uper template me dynamic id index 2-index3)
						var id_array = $(this)[0].id.split(",");
						console.log($(this)[0].id); // is se id print ho rahi hy sites and dates ki //$(this)[0].id just like 3-0

						var id_array = id_array[0].split("-"); // console.log("id pickup |%s| |%s|", id_array[0], id_array[1]);

						var x = id_array[0].trim();
						var y = id_array[1].trim();

						var x = parseInt(x);
						var y = parseInt(y);
						var old_record = Window.storeArr[x][y];
						old_record = Window.storeArr[x][y];
						old_record = Window.storeArr[x][y];
						console.log(Window.storeArr[x][y], "old_record");

						var datalist = $(this).text();
						console.log("datalist $(this).text()", datalist); //drag value pick just like 0 Aqsa khan 9:30 18:00

						var new_arr = datalist.split(" "); //split space
						console.log(new_arr, "full record  new_arr ");

						var new_x = new_arr[0].trim();
						console.log(new_arr[0].trim(), " new_x    new_arr[0].trim()"); //space remove in thisline	
						// var WindowstoreArr =$.trim(Window.storeArr[x][y].replace(/[\t\n]+/g,' ')); //is se space khtm 	

						var new_record = that.arrBackLog[new_x];
						console.log([new_record], "new_record");

						let arr3 = [new_record].map((item, i) => Object.assign({}, item, that.arrBackLog[new_x]));    ///object
						console.log(arr3, "arr3 new_record conver into object ");

						Window.storeArr[x][y] = arr3;

						var value_new_data = Window.storeArr[x][y];
						//  var value_new_data=[];
						// value_new_data.push(Window.storeArr[x][y]) /// value store ki 
						console.log(value_new_data, "value_new_data value_new_data.push(indow.storeArr[x][y]) ")


						const merge = value_new_data.concat(old_record)
						console.log(merge, " merge old_rec and value_new_data")
						Window.storeArr[x][y] = merge;
						console.log("recently combined aray", Window.storeArr[x][y] = merge)
						//value  store in array
						console.log(Window.storeArr, "Window.storeArr");


						$("#" + $(this)[0].id + " .officers_tbody").append(
							"<tr><td class='td_officer_name'><span class='officer-name' id='officer_name'>" +
							new_record["name"] +
							"</span></td></tr>"
						);
						office_name_store = new_record["name"];

						console.log("$(this)[0].id", $(this)[0].id);

						that.setvalueinmodal(
							new_record["start"],
							new_record["end"],
							$(this)[0].id, // is se id print ho rahi hy sites and dates ki //$(this)[0].id just like 3-0 //modal id
							new_record["jobid"]
						); //time function call

					},

					
				})

				.draggable(false);
			$("#sortable1, #sortable2").disableSelection();
		});

		// 	edited Time value (updated buton popup button)
		$(function () {
			$(".Save_editedbutton" + that.modalid).click(function (mdid_) {
				console.log(".Save_editedbutton" + that.modalid);
				console.log("rcid received", rc_id);//overlay color id 
				$("#" + rc_id).remove();
				console.log("rcid hide", rc_id);
				mdid_ = that.modalid;
				console.log("666666666666666666666666666666666666666", mdid_);
				var startjb_ = document.querySelector(
					"#myPopover2" + mdid_ + " #startTime"
				).value;

				console.log(startjb_, "startjb_ ");

				var endjb_ = document.querySelector(
					"#myPopover2" + mdid_ + " #endTime"
				).value;
				console.log(endjb_, "endjb_ ");

				// Today date started time full day time
				var Todaydatestart = moment();
				Todaydatestart = Todaydatestart.startOf("day");
				console.log("Todaydatestart " + Todaydatestart.startOf("day"));

				// job Started time
				var jobstart = new Date(that.current_date + " " + startjb_);
				console.log(jobstart, "jobstart");
				var job__start = jobstart.toString().substring(16, 24);

				console.log(job__start, "jobstarttoString()substring");

				// job End time
				var jobend = new Date(that.current_date + " " + endjb_);
				console.log(jobend, "jobend");

				var job__end = jobend.toString().substring(16, 24);

				console.log(job__end, "jobendtoString()substring");

				//start job diff started day

				var difftime = jobstart - Todaydatestart;
				that._diff_time = difftime;
				console.log(that._diff_time, "this._diff_time");
				console.log(difftime, " difftime ");
				var _diff_time = that.millisToMinutesAndSeconds(difftime);
				console.log(_diff_time, "_diff_time");

				// job  end day  diff
				var durationjob = jobend - Todaydatestart;
				that._duration_job = durationjob;
				console.log(that._duration_job, "this._duration_job");
				console.log(durationjob, "durationjob ");
				var _duration_job = that.millisToMinutesAndSeconds(durationjob);
				console.log(_duration_job, "_duration_job ");

				// convert time
				var JOb_Start_ = that._diff_time;
				var JOb_End_ = that._duration_job;

				function msToTime(duration) {
					var milliseconds = parseInt(duration % 1000),
						seconds = parseInt((duration / 1000) % 60),
						minutes = parseInt((duration / (1000 * 60)) % 60),
						hours = parseInt((duration / (1000 * 60 * 60)) % 24);

					hours = hours < 10 ? "0" + hours : hours;
					minutes = minutes < 10 ? "0" + minutes : minutes;
					seconds = seconds < 10 ? "0" + seconds : seconds;
					return hours + ":" + minutes + ":" + seconds;
				}

				JObStart = msToTime(JOb_Start_);
				JObEnd = msToTime(JOb_End_);
				console.log(JObStart, JObEnd)

				var randomColor = Math.floor(Math.random() * 16777215).toString(16);
				//color overlay div
				var div_element =
					'<div id="' +
					rc_id +
					'" class="time_overlay" style="left:' +
					_diff_time * 2 * 2 +
					"px;position:relative;height:40px;background:#" +
					randomColor +
					";width:" +
					(_duration_job - _diff_time) * 2 * 2 +
					'px; top:-2px; cursor: pointer;"></div>';
				console.log(div_element, "div_element");

				$("#" + mdid_ + " .sessionbox").append(div_element);
				// document.getElementById('masterdiv').innerHTML = '';

				// edited modal open when we click color overlay
				$("#" + rc_id).click(function (e) {
					console.log(rc_id);
					console.log("click update function ");
					var left = "1359px";
					var top = "370px";
					console.log(left, top);
					var b1 = (document.getElementById(
						"myPopover2" + mdid_
					).style.cssText =
						"display: block; position: absolute;left:" +
						left +
						";top:" +
						top +
						"");
					// time edit value pick

					document.querySelector("#myPopover2" + mdid_ + " #startTime").value =
						job__start;
					// console.log(job__start);

					document.querySelector("#myPopover2" + mdid_ + " #endTime").value =
						job__end;

					// console.log(job__end);
				});
				document.getElementById("myPopover2" + mdid_).style.cssText = "display: none;";
				// time edit value pick
			});
		});

		// save data modal inner button
		$(function () {
				$(".Save_data" + that.modalid).click(function () {
					//   mdid_ = that.modalid;
					console.log(".Save_data" + that.modalid);
					// mili sec convert into hours
					var ofname = [];
					const elements1 = document.querySelectorAll(
						".combined_table .officer-name"
					);
					elements1.forEach(function (element) {
						ofname.push(element.innerHTML);
					});
					var ofjob = [];
					var i = 0;
					const elements2 = document.querySelectorAll(
						".combined_table .time_overlay"
					);
					elements2.forEach(function (element) {
						ofjob.push({
							name: ofname[i],
							left: element.style.left,
							width: element.style.width,
						});

						var sav_data =
							`<li class="portlet ui-state-default">
																<span class="portlet-header" id="officer_name">` +
							ofname[i] +
							`</span>
																<span class="portlet-content" id="officer_date"
																	> ` +
							element.style.left +
							"-" +
							element.style.width +
							`</span
																>
															</li>`;
						console.log(element.style.left);
						console.log(element.style.width);
						console.log(ofname[i]);
						i++;
					});
					console.log(ofjob);
				});
			});
	},
	methods: {
		submitFilters() {
			this.isSubmitted = true;
			this.fetchData();
		},
		fetchData() {
			let dataToPost = {};
			// this.isLoading = true;
		},
		resetFilterForm() {
			this.isSubmitted = false;
			this.fetchData();
		},
		// outerbuttonsave in getitemlocalstorage
		SaveData_view() {
			// var data2 = localStorage.getItem("textdata");
			// let obj = JSON.parse(data2);
			// console.log(obj, "**************************localStorage****************************");

		},
		// outerbuttonsave in setitemlocalstorage
		SaveChanges() {
			// var json = JSON.stringify(Window.storeArr);
			// var persistentData = window.localStorage;
			// var data = persistentData.setItem("textdata", json);
			// console.log(Window.storeArr, "data")








		},
		// plot difference b/w start and end job
		millisToMinutesAndSeconds(millis) {
			var minutes = Math.floor(millis / 60000);
			return minutes;
		},
		//
		setvalueinmodal(jobstart, jobend, modal_id, job_id) {
			console.log(this.current_date);
			this.modalid = modal_id;

			// creates a table row timeline small boxes
			var tempdata = "";
			tempdata += "<tr>";
			tempdata += "<td>";
			tempdata += "<div>";
			for (var j = 0; j < 24 * 60; j++) {
				tempdata +=
					"<span id='events_content'" + j + " class='events_content'></span>";
			}
			tempdata += "</div>";
			tempdata += "</td>";
			tempdata += "</tr>";
			console.log(modal_id);
			$("#" + modal_id + " .time_body").append(tempdata);

			// Today date started time full day time
			var Todaydatestart = moment();
			Todaydatestart = Todaydatestart.startOf("day");
			console.log("Todaydatestart " + Todaydatestart.startOf("day"));

			// job Started time
			jobstart = new Date(this.current_date + " " + jobstart);
			console.log(jobstart, "jobstart");
			var job__start = jobstart.toString().substring(16, 24);
			console.log(job__start, "jobstarttoString()substring");

			// job End time
			jobend = new Date(this.current_date + " " + jobend);
			console.log(jobend, "jobend");

			var job__end = jobend.toString().substring(16, 24);
			console.log(job__end, "jobendtoString()substring");

			//start job diff started day

			var difftime = jobstart - Todaydatestart;
			this._diff_time = difftime;

			console.log(this._diff_time, "this._diff_time");
			var _diff_time = this.millisToMinutesAndSeconds(difftime);
			console.log(_diff_time, "_diff_time");

			// job  end day  diff
			var durationjob = jobend - Todaydatestart;
			this._duration_job = durationjob;
			console.log(this._duration_job, "this._duration_job");


			var _duration_job = this.millisToMinutesAndSeconds(durationjob);
			console.log(_duration_job, "_duration_job ");


			var JOb_Start = this._diff_time;
			var JOb_End = this._duration_job;
			function msToTime(duration) {
				var milliseconds = parseInt(duration % 1000),
					seconds = parseInt((duration / 1000) % 60),
					minutes = parseInt((duration / (1000 * 60)) % 60),
					hours = parseInt((duration / (1000 * 60 * 60)) % 24);

				hours = hours < 10 ? "0" + hours : hours;
				minutes = minutes < 10 ? "0" + minutes : minutes;
				seconds = seconds < 10 ? "0" + seconds : seconds;
				return hours + ":" + minutes + ":" + seconds;
			}
			JObStart = msToTime(JOb_Start);
			JObEnd = msToTime(JOb_End);
			console.log(JObStart, JObEnd)
			//	for (var i = difftime; i < durationjob; i++) {
			// $("#events_content" + i).css("background-color", "yellow");
			// console.log("forloop run", i);

			var randomColor = Math.floor(Math.random() * 16777215).toString(16);
			//color overlay div
			var div_element =
				'<div id="time_overlay_' +
				randomColor +
				'" class="time_overlay" style="left:' +
				_diff_time * 2 * 2 +
				"px;position:relative;height:40px;background:#" +
				randomColor +
				";width:" +
				(_duration_job - _diff_time) * 2 * 2 +
				'px; top:-2px; cursor: pointer;"></div>';
			console.log(div_element, "div_element");

			$("#" + modal_id + " .sessionbox").append(div_element);

			// edited modal open when we click color overlay

			$("#time_overlay_" + randomColor).click(function (e) {
				console.log("click");
				rc_id = "time_overlay_" + randomColor;
				console.log("random_Color_id", rc_id);
				var left = "1359px";
				var top = "370px";
				console.log(left, top);
				var b1 = (document.getElementById(
					"myPopover2" + modal_id
				).style.cssText =
					"display: block; position: absolute;left:" +
					left +
					";top:" +
					top +
					"");
				// time edit value pick

				document.querySelector("#myPopover2" + modal_id + " #startTime").value =
					job__start;


				document.querySelector("#myPopover2" + modal_id + " #endTime").value =
					job__end;

			});
			document.getElementById("myPopover2" + modal_id).style.cssText =
				"display: none;";

			// edited modal closed
			$(".close_editedbutton").click(function () {
				$(".my_Popover").hide();
				console.log(" edited modal closed");
			});
		},

		//  click modal display
		Showmodal: function (event) {
			var modal_button = event.target.id;
			var dyid_split = modal_button.split("_");
			console.log(dyid_split[1], " id split");
			console.log("Showmodal click button function run");

			document.getElementById(dyid_split[1]).style.display = "block";
			$(".close_button").click(function () {
				$(".modal_box").hide();
				console.log(" Showmodal() function modal close");
			});
		},
		//  used as a on generate modal display
		generatemodal(id) {
			this.generateid = id;
			console.log("generatemodal run", this.generateid);

			var tempmodal = "";
			tempmodal +=
				'<div class="modal fade show modal_box" backdrop="static" id="' +
				id +
				'" style="display:none;" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true" >';
			tempmodal +=
				'  <div class="modal-dialog modal-dialog-centered modal-lg" role="document">';
			tempmodal += '    <div class="modal-content">';
			tempmodal += '      <div class="modal-header">';
			tempmodal +=
				'        <h5 class="modal-title" id="exampleModalLongTitle">Time Schedule</h5>';
			tempmodal +=
				'<button type="button" class="close button-close-modal" onClick="document.getElementById(\'' +
				id +
				"').style.display = 'none'\" data-dismiss=\"modal\">&times;</button>";
			tempmodal += "        </button>";
			tempmodal += "      </div>";
			tempmodal += '      <div class="modal-body">';

			tempmodal += '		<div class="combined_table">';
			tempmodal +=
				'<table class="tableofficer table table-striped table-bordered">';
			tempmodal += '				<thead class="thead-inverse">';
			tempmodal += '				<tr class="officer_box">';
			tempmodal += '					<th class="officer_title">';
			tempmodal += '						<span class="officer_name"> Officer Name </span>';
			tempmodal += "					</th>";
			tempmodal += "				</tr>";
			tempmodal += "			</thead>";
			tempmodal += '			<tbody class="officers_tbody"></tbody>';
			tempmodal += "		</table>";
			tempmodal += '	<div class="table-center scrollbar" id="style-1">';
			tempmodal +=
				'	<table class="tabletime table table-striped table-bordered">';
			tempmodal += '	<div class="sessionbox">';

			tempmodal += "</div>";
			tempmodal += '	<thead class="thead-_inverse">';
			tempmodal += '	<tr class="time_line_slide">';
			tempmodal += "	<th>";
			tempmodal += "	<div class='time-span'>";
			$.each(this.timeline, function (key, value) {
				tempmodal +=
					'<span class="time_title" colspan="30" id="time_title"><div class="timebox_">' +
					value +
					"</div></span>";
			});
			tempmodal += "	</div>";
			tempmodal += "		</th>";
			tempmodal += "	</tr>";
			tempmodal += "	</thead>";
			tempmodal += '<tbody class="time_body"></tbody>';
			tempmodal += "	</table>";
			tempmodal += "		</div>";

			tempmodal += "      </div>";
			tempmodal += '      <div class="modal-footer">';
			tempmodal +=
				'        <button type="button" class="btn btn-secondary close_button" data-dismiss="modal">Close</button>';
			tempmodal +=
				' <button type="button" class="btn btn-primary Save_data">Save </button>';
			tempmodal += "      </div>";
			tempmodal += "    </div>";
			tempmodal += "  </div>";
			tempmodal += "</div>";
			tempmodal +=
				` <div id="myPopover2` +
				id +
				`" class="popover popover-x popover-default popover_box my_Popover" style="display:none">
																									                    <div class="arrow"></div>`;

			tempmodal +=
				'<h3 class="popover-header popover-title"><span class="close pull-right" onClick="document.getElementById(\'myPopover2' + id + "').style.display = 'none'\" data-dismiss=\"popover-x\">&times;</span>Time Update</h3>";
			tempmodal += `   <div class="popover-body popover-content">					     
																												<div class="time_edit_box-inner">
																													<div class="row">
																														<div class="col-sm-12">
																															<label><b>Job Start</b></label>
																															<div class="row" style="display: flex; width: 118%">
																																<div class="col-md-6 col-sm-12">
																																	<div class="" id="startTime_div">
																																		<input
																																			placeholder="Time"
																																			type="time"
																																			autocomplete="off"
																																			class="form-control"
																																			name="startTime"
																																			id="startTime"
																																			value=""
																																		/>
																																	</div>
																																</div>
																															</div>
																														</div>
																													</div>
																													<br />
																													<div class="row">
																														<div class="col-sm-12">
																															<label><b>Job End</b></label>
																															<div class="row" style="width: 118%">
																																<div class="col-md-6 col-sm-12">
																																	<div class="" id="endTime_div">
																																		<input
																																			placeholder="Time"
																																			type="time"
																																			autocomplete="off"
																																			class="form-control"
																																			name="endTime"
																																			id="endTime"
																																			value=""
																																		/>
																																	</div>
																																</div>
																															</div>
																														</div>
																													</div>
																												</div>
																											</div>`;
			tempmodal += '  <div class="popover-footer">';
			tempmodal +=
				'  <button type="submit" class="btn btn-sm btn-primary Save_editedbutton">update</button>';
			// tempmodal +='  <button type="submit" class="btn btn-sm btn-primary Save_editedbutton" onClick="editeTimevalue(\'' +id +"').style.display = 'block'\">update</button>";
			tempmodal +=
				'  	<button type="button" class="btn btn-sm btn-primary close_editedbutton" >cancel</button>';
			tempmodal += "    </div>";
			tempmodal += "    </div>";
			document.querySelector("#modal_container").innerHTML += tempmodal;
		},

		// empty array aur make2DArray jo hai nextweek() me call ho raha hy
		make2DArray(w, h, val) {
			var arr = [];
			for (let i = 0; i < h; i++) {
				arr[i] = [];
				for (let j = 0; j < w; j++) {
					arr[i][j] = val;
				}
			}
			return arr;
		},
		// is me hum jo hai value ko store karwate hai just like {site,date} site ka name and date(7) {name: 'a', date: '2022-03-29'}date: "2022-03-29"name: "a"[[Prototype]]: Object ' storeArry()'
		storeArry() {
			// var modal_runtime = function () {
			// var site_size = Object.keys(that.noofsites).length;
			// console.log( site_size );//4
			// var nextWeekDates_=that.nextWeekDates.length;
			// console.log(nextWeekDates_);//7
			// var  modal_set=site_size * nextWeekDates_
			// console.log(modal_set)//28

			this.storeArr = [];
			var i = 0;
			for (const [key, value] of Object.entries(this.noofsites)) {
				var j = 0;
				this.storeArr[i] = [];

				for (var k = 0; k < this.nextWeekDates.length; k++) {
					this.generatemodal(i + "-" + j);
					this.storeArr[i][j] = {
						// name: value.title,
						name: value.name,
						date: this.nextWeekDates[k],
						sort:Window.storeArr
					};
					console.log({ name: value.name, date: this.nextWeekDates[k],sort:Window.storeArr});
					j++;
				}
				i++;
			}
		
			console.log(this.storeArr, "this.storeArr******************************");
			var json = JSON.stringify(Window.storeArr);
			var persistentData = window.localStorage;
			var data = persistentData.setItem("textdata", json);
			console.log(Window.storeArr, "data")
		},

		// is se grid generate hoti hai jab hum above option se jab hum select karte hai time just like period ,lastmonth
		griddate() {
			var start = moment(this.form.startDate, "YYYY-MM-DD");
			var end = moment(this.form.endDate, "YYYY-MM-DD");

			//Difference in number of days
			this.noofdays = parseInt(moment.duration(end.diff(start)).asDays());
			console.log(this.noofdays);

			var nextdate = start;
			this.nextWeekDates = [];
			if (this.noofdays == 0) {
				this.noofdays = 1;
				this.nextWeekDates.push(nextdate);
			} else {
				this.noofdays++;
				for (var i = 0; i < this.noofdays; i++) {
					var nextdate2 = moment(nextdate)
						.clone()
						.add(i, "days")
						.format("YYYY-MM-DD");
					this.nextWeekDates.push(nextdate2);
					// console.log(nextdate2);
				}
			}
		},
		//is se grid bante hai
		nextweek() {
			this.nextWeekDates = [];
			var nextweekstart = moment().add(1, "weeks").startOf("isoWeek");
			var nextweekend = moment().add(1, "weeks").endOf("isoWeek");
			var day = nextweekstart.clone();

			for (var k = 0; k < 7; k++) {
				// for( var k=0; k<nextweekend; k++)
				// console.log( new Date(day));
				this.nextWeekDates.push(day.format("YYYY-MM-DD"));
				day = day.clone().add(1, "d");
			}
			// console.log(this.nextWeekDates);
			this.noofdays = this.nextWeekDates.length;

			Window.storeArr = this.make2DArray(
				this.nextWeekDates.length,
				Object.keys(this.noofsites).length,
				0
			);
		},
		dateCalculation(type) {
			this.form.custom = false;
			// this.div_show = false;
			if (type.id === "custom") {
				this.form.startDate = null;
				this.form.endDate = null;
				this.form.custom = true;
				// this.div_show = true;
				this.isSubmitted = false;

				var coll = $("#advance_filter"); // it open advance filter when you select userr define fromm drop down
				var i;
			} else {
				var date = new Date();
				if (type.id === "yesterday") {
					let startdate = new Date(date.setDate(date.getDate() - 1));
					this.form.startDate = dateFormat(
						startdate.setHours(0, 0, 0, 0),
						"isoDateTime"
					);
					this.form.endDate = dateFormat(
						date.setHours(23, 59, 59),
						"isoDateTime"
					);
				} else if (type.id === "today") {
					let startdate = new Date();
					this.form.startDate = dateFormat(
						startdate.setHours(0, 0, 0, 0),
						"isoDateTime"
					);
					this.form.endDate = dateFormat(
						date.setHours(23, 59, 59),
						"isoDateTime"
					);
				} else if (type.id === "this_week") {
					let startDate = this.getMondayThisWeek(date);
					this.form.startDate = dateFormat(
						startDate.setHours(0, 0, 0, 0),
						"isoDateTime"
					);
					this.form.endDate = dateFormat(
						date.setHours(23, 59, 59),
						"isoDateTime"
					);
				} else if (type.id === "last_week") {
					let startDate = this.getMondayForWeek(date);
					let toDate = this.addDays(startDate, 6);
					let formattedStartDate = dateFormat(
						startDate.setHours(0, 0, 0, 0),
						"isoDateTime"
					);
					let formattedToDate = dateFormat(
						toDate.setHours(23, 59, 59),
						"isoDateTime"
					);
					this.form.startDate = formattedStartDate;
					this.form.endDate = formattedToDate;
				} else if (type.id === "this_month") {
					let firstDayOfMonth = new Date(
						date.getFullYear(),
						date.getMonth(),
						1
					);
					this.form.startDate = firstDayOfMonth;
					this.form.endDate = dateFormat(
						date.setHours(23, 59, 59),
						"isoDateTime"
					);
				} else if (type.id === "last_month") {
					let firstDayOfMonth = new Date(
						date.getFullYear() - (date.getMonth() > 0 ? 0 : 1),
						(date.getMonth() - 1 + 12) % 12,
						1
					);
					let lastDayOfMonth = new Date(date.getFullYear(), date.getMonth(), 0);
					let formattedStartDate = dateFormat(firstDayOfMonth, "isoDateTime");
					let formattedToDate = dateFormat(
						lastDayOfMonth.setHours(23, 59, 59),
						"isoDateTime"
					);
					this.form.startDate = formattedStartDate;
					this.form.endDate = formattedToDate;
					console.log(this.form.startDate);
					console.log(this.form.endDate);
				} else if (type.id === "nextweek") {
					this.nextweek();
					this.form.startDate = dateFormat(
						this.nextWeekDates[0],
						"isoDateTime"
					);
					this.form.endDate = dateFormat(this.nextWeekDates[6], "isoDateTime");
				}
				this.fetchData();
			}
		},
		addDays(date, days) {
			var result = new Date(date);
			result.setDate(result.getDate() + days);
			return result;
		},
		getMondayThisWeek(d) {
			d = new Date(d);
			var day = d.getDay(),
				diff = d.getDate() - day + (day == 0 ? -6 : 1); // adjust when day is sunday
			// return new Date(d.setDate(diff - 7));
			return new Date(d.setDate(diff));
		},
		getMondayForBiWeek(d) {
			d = new Date(d);
			var day = d.getDay(),
				diff = d.getDate() - day + (day == 0 ? -6 : 1); // adjust when day is sunday
			return new Date(d.setDate(diff - 14));
		},
		formatDate(d) {
			let mydate = this.$root.server_time_convert(d);
			return dateFormat(mydate, "dd-mm-yyyy");
		},
		formatTime(d) {
			let mytime = this.$root.server_time_convert(d);
			return dateFormat(mytime, "isoTime");
		},
		formatDateTime(d) {
			let date = this.formatDate(d);
			let time = this.formatTime(d);
			return date + " " + time;
		},
		fetchData() {
			let dataToPost = {
				start_date: this.form.startDate ? this.form.startDate : null,

				end_date: this.form.endDate ? this.form.endDate : null,
			};
		},
	},
};
var rc_id = "";
var JObStart = "";
var JObEnd = "";
var office_name_store = "";
function closemodal(id) {
	document.getElementById(id).style.display = "none";
}
// isko hum ne islye outside rakha hy k hum usko vuejs me aur jquery me bhi call kar sakte hai

Window.storeArr = [];
var abcde = [];
</script>
<style>
/* save button */
.Drop_heading {
	text-align: center;
	background-color: lightblue;
	padding: 5px;
	font-size: 7px;
	margin: 0px
}

.Save_Change_btn {
	background: #092d749e;
	margin: 0px;
	padding: 7px;
	color: #dbdfff !important;
	text-shadow: 1px 2px 3px #545a6e;
	letter-spacing: 0.8px;
	box-shadow: 1px 2px 3px #96a4c0;
	width: 15%;
}

.save_button_div {
	display: flex;
	justify-content: center;
	margin-bottom: 21px;
}

.Save_Change_btn:hover {
	background: #092d749e !important;
}

/* Dragdrop box */

.ddcontainer {
	/* margin-left: 70px;
				margin-right: 70px; */
}

.dragdrop {
	margin-top: 65px;
	margin-bottom: 65px;
	/* margin-left: 50px;
				margin-right: 50px;  */
}

.gridwrapper {
	display: flex;
	overflow-x: auto;
	flex-direction: column;
	flex-wrap: wrap;
	border: 1px solid #b9c1d769;
}

.datebox {
	display: inline-flex;
	flex-wrap: nowrap;
}

.emptydiv {
	background: #f6f7fd6e;
	width: 172px;
}

.date {
	background: #fbfcfe;
	color: #8f919f;
	letter-spacing: 0.8px;
	font-weight: 600;
	border: 1px solid #acb3d14a;
	padding: 13px;
	min-width: 199px;
	display: flex;
	flex-direction: row;
	flex-wrap: nowrap;
	align-content: center;
	justify-content: space-evenly;
	align-items: center;
}

.outergrid {
	display: flex;
	flex-direction: row;
	flex-wrap: nowrap;
}

.dragdata {
	display: flex;
}

.innergrid {
	border: 1px solid #b9c1d473;
	display: inline-block;
	background: #f6f7fd;
	line-height: 0px;
}

/* qUEUE LIST */
.queuemaincontainer {
	margin-left: 50px;
	margin-right: 50px;
}

.queueC_ontainer_ {
	display: -ms-flexbox;
	display: flex;
	background: #e8ecfa66;
	/* background: #f6f8fa; */
	border: 1px solid #d5dbfc54;
	flex-direction: column;
	flex-wrap: wrap;
	padding: 7px;
}

.scrumboard {
	display: inline-block;
	border: 1px solid #f0f0f1;
	box-shadow: 2px 2px 8px #b5bcd2;
	/* background: #fcfcfe; */
	background: #f9fafe;
}

.QueueList {
	/* display: inline-grid;
			grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr; */
	display: flex;
}

.heading_jobQueue {
	font-size: 30px;
	font-weight: 600;
	color: #8f919f;
	text-shadow: 1px 2px 3px #bcc4e1;
	letter-spacing: 0.8px;
}

#sortable1,
#sortable2,
#sortable3 {
	list-style-type: none;
}

.queueBox {
	border: 1px solid #d5dbfc54;
	padding: 12px;
}

.ui-widget.ui-widget-content {
	border: 1px solid #dde0ec;
	background: #eceffa;
	display: flex;
	align-items: center;
	flex-direction: row;
}

.portlet {
	/* margin: 0 1em 1em 0; */
	padding: 2.3px;
}

.sitesbox {
	display: flex;
	color: #8f919f;
	letter-spacing: 0.8px;
	border: 1px solid #caccd7;
	font-size: 10px;
	border: 1px solid #c7cfe63d;
	background: #f6f7fd;
	min-height: 63px;
	min-width: 172px;
	padding: 0px;
	font-weight: 600;
	text-align: center;
	align-items: center;
	justify-content: center;
}

.officername {
	text-align: center;
	border: 1px solid #dddddd;
	background: #f8fafc;
	display: flex;
	vertical-align: middle;
	color: #8f919f;
	min-height: 18px;
	font-weight: bold;
	box-shadow: 1px 1px 4px #b6bcd3;
	text-shadow: 1px 2px 3px #bcc4e1;
	align-items: center;
}

.portlet-header {
	padding: 0.2em 0.3em;
	margin-bottom: 0.2em;
	font-size: 10px;
	position: relative;
	pointer-events: all;
}

.portlet-content {
	padding-left: 2px;
	font-size: 11px;
	font-weight: 600;
	color: #8f919f;
	text-shadow: 1px 2px 3px #bcc4e1;
}

.portlet-toggle {
	position: absolute;
	top: 50%;
	right: 0;
	margin-top: -8px;
}

.portlet-placeholder {
	border: 1px dotted black;
	margin: 0 1em 1em 0;
	height: 50px;
}

.dropfalse {
	pointer-events: none;
	width: 197px;
	min-height: 39px;
	min-width: 178px;
	padding: inherit;
	/* border: 1px solid rgb(238 240 247); */
	border: 1px solid rgb(189 190 195);
	background: #cdcfdc91;
}

.btn_modal_detail {
	background: #cdcfdc;
	margin: 0px;
	padding: 7px;
	text-align: center;
	display: flex;
	color: #626576 !important;
	text-shadow: 1px 1px 2px #aaaec5;
	letter-spacing: 0.8px;
	align-items: center;
	box-shadow: 1px 2px 3px #96a4c0;
	width: 100%;
	justify-content: center;
}

.btn_modal_detail:hover {
	background: #cdcfe0 !important;
	color: #626576 !important;
}

/* modal */

.modal-title {
	text-align: center;
	display: flex;
	vertical-align: middle;
	color: #8f919f;
	font-weight: bold;
}

.modal-footer {
	margin-top: 40px;
	padding-top: 12px;
}

.close_button {
	background: #092d749e;
	margin: 0px;
	padding: 7px;
	color: #dbdfff !important;
	text-shadow: 1px 2px 3px #545a6e;
	letter-spacing: 0.8px;
	box-shadow: 1px 2px 3px #96a4c0;
	width: 120px;

}

.close_button:hover {
	background: #092d749e !important;
}

.Save_data {
	width: 120px;
	background: #092d749e;
	margin: 0px;
	padding: 7px;
	color: #dbdfff !important;
	text-shadow: 1px 2px 3px #545a6e;
	letter-spacing: 0.8px;
	box-shadow: 1px 2px 3px #96a4c0;
}

.Save_data:hover {
	background: #092d749e !important;
}

.popover-header {
	text-align: center !important;
	display: flex !important;
	vertical-align: middle !important;
	color: #8f919f !important;
	font-weight: bold !important;
}

.Save_editedbutton {
	width: 78px;
	background: #092d749e;
	margin: 0px;
	padding: 7px;
	color: #dbdfff !important;
	text-shadow: 1px 2px 3px #545a6e;
	letter-spacing: 0.8px;
	box-shadow: 1px 2px 3px #96a4c0;
}

.Save_editedbutton.hover {
	background: #092d749e !important;
}

.close_editedbutton {
	width: 78px;
	background: #092d749e;
	margin: 0px;
	padding: 7px;
	color: #dbdfff !important;
	text-shadow: 1px 2px 3px #545a6e;
	letter-spacing: 0.8px;
	box-shadow: 1px 2px 3px #96a4c0;
}

.close_editedbutton:hover {
	background: #092d749e !important;
}

.close_editedbutton {
	margin-bottom: 0px !important;
}

.Save_editedbutton {
	margin-bottom: 0px !important;
}

div#startTime_div {
	width: 213px;
}

div#endTime_div {
	width: 213px;
}

.close-modal {
	position: absolute;
	top: 7px;
	right: 7px;
}

.tabletime {
	position: relative;
	width: 100%;
	display: contents;
	/* overflow: auto; */
	/* margin-top: -22px !important;*/
}

.table-center {
	position: relative;
	/* table-layout: fixed; */
	display: block;
	/* table-layout: fixed; */
	/* width: 100px; */
	overflow-x: auto;
}

.tabletime th,
.tabletime td {
	padding: 0px !important;
}

.sessionbox {
	width: 100%;
	position: absolute;
	z-index: 10;
	top: 45px;
}

.combined_table {
	position: relative;
	display: -ms-flexbox;
	display: flex;
}

.table-center {
	/* overflow-x: scroll;
										overflow-y: scroll; */
	position: relative;
}

li {
	list-style: none;
}

/* .modal{																										
			background: rgb(0 0 0 / 75%);
			} */
th.officer_title {
	padding: 0px !important;
	height: 41px;
}

div#event-container {
	display: flex;
	flex-direction: row;
}

.table_body {
	display: flex;
	flex-direction: row;
}

.office_title_item {
	width: auto;
	min-width: 172px;
}

.table_head {
	display: flex;
}

.time_title {

	min-width: 120px;
	height: 40px;
	display: table-cell;
	background: #f6f7fd;
	border: 1px solid #d0d5e5c2;
	text-align: center;
	vertical-align: middle;
	color: #8f919f !important;
	text-shadow: 1px 1px 2px #cccdd1 !important;
}

.table-bordered thead th {
	border-bottom-width: 1px !important;
}

.modal-right {
	float: right;
}

.time_edit_box {
	float: right;
	overflow: hidden;
	background: #f0e68c;
}

/* .time_edit_box-inner {
									width: 400px;
									padding: 10px;
									border: 1px solid #a29415;
									} */

.gdd {
	width: 440px;
	padding: 4px;
}

.events_content {
	/* padding: 2.32px !important; */
	width: 4px;
	display: inline-block;
	float: left;
	height: 40px;
	border-right: 1px solid #d0d0d0;
}

.tableofficer {
	min-width: 191px;
}

td.td_officer_name {
	background: #f6f7fd;
	font-size: 12px;
	font-weight: 600;
	color: #8f919f !important;
}

tr.officer_box {
	background: #f6f7fd;
	font-size: 15px;
	font-weight: 600;
	color: #626576 !important;
	text-shadow: 1px 1px 2px #cccdd1 !important;
}


#style-1::-webkit-scrollbar-track {
	-webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
	border-radius: 10px;
	background-color: #F5F5F5;
}

#style-1::-webkit-scrollbar {
	padding: 0px;
	height: 12px;
	width: 12px;
	background-color: #F5F5F5;
}

#style-1::-webkit-scrollbar-thumb {
	border-radius: 10px;
	-webkit-box-shadow: inset 0 0 6px #cdced3d6;
	background-color: #667ca9a3;
}
</style>
