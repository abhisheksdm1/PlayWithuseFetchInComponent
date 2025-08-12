````
import React, { useState } from "react";
import {
  LineChart,
  Line,
  XAxis,
  YAxis,
  CartesianGrid,
  Tooltip,
  ResponsiveContainer,
  AreaChart,
  Area,
  Legend,
} from "recharts";
import { useFetch } from "../Hook/useFetch";

const pointsData = [
  { week: "W1", points: 100 },
  { week: "W2", points: 80 },
  { week: "W3", points: 70 },
  { week: "W4", points: 60 },
  { week: "W5", points: 65 },
  { week: "W6", points: 75 },
  { week: "W7", points: 90 },
];

const Card = ({ title, value, subtitle }) => (
  <div className="bg-[#0C2A27] rounded-xl p-4 text-white w-full shadow-sm ">
    <h4 className="text-sm text-gray-300">{title}</h4>
    <p className="text-2xl font-semibold mt-1">{value}</p>
    <p className="text-xs text-green-400 mt-1">{subtitle}</p>
  </div>
);

export default function AnalyticsInsight() {
  const { data, loading, error } = useFetch("A-dashboard/revenue-impact");

  const now = new Date();
  const year = now.getFullYear();
  const month = String(now.getMonth() + 1).padStart(2, "0");
  const day = String(now.getDate()).padStart(2, "0");
  const currentDate = `${year}-${month}-${day}`;
  const firstDate = `${year}-${month}-01`;

  const [startDate, setStartDate] = useState(firstDate);
  const [endDate, setEndDate] = useState(currentDate);
  const [dateRangeUrl, setDateRangeUrl] = useState(
    `A-dashboard/avg-purchase-size/1/${firstDate}/${currentDate}`
  );

  const [revenueImpact, setRevenueImpact] = useState(
    `A-dashboard/monthly-revenue-impact/1/${firstDate}/${currentDate}`
  );
  console.log("Date Range URL:", dateRangeUrl);

  const {
    data: purchaseData,
    loading: purchaseLoading,
    error: purchaseError,
    refetch: refetchPurchaseData,
  } = useFetch(dateRangeUrl);

  const {
    data: revenueImpactData,
    loading: revenueImpactDataLoad,
    error: revenueImpactDataError,
    refetch,
  } = useFetch(revenueImpact);

  const revenueData = revenueImpactData?.data || [];
  console.log("Revenue Data:", revenueData);
  return (
    <div className="p-4 md:p-8 font-sans bg-primary min-h-screen text-white space-y-6">
      <div className="flex flex-col  md:flex-row justify-between items-center mb-6 gap-4">
        <div className="flex flex-col gap-2 mb-5">
          <h2>Analytics & Insights</h2>
          <h4>
            Advanced analytics to understand program performance and customer
            behavior
          </h4>
        </div>
        <div className="flex flex-col md:flex-row gap-1 items-center justify-center ">
          {/* <h4>Date Range</h4> */}
          <input
            type="date"
            value={startDate}
            onChange={(e) => setStartDate(e.target.value)}
            className="rounded-[12px] text-[16px] font-medium border pt-[10px] pr-[16px] pb-[10px] pl-[16px]"
            placeholder="mm/dd/yyyy"
          />
          <input
            type="date"
            value={endDate}
            onChange={(e) => setEndDate(e.target.value)}
            className="rounded-[12px] text-[16px] font-medium border pt-[10px] pr-[16px] pb-[10px] pl-[16px]"
            placeholder="mm/dd/yyyy"
          />
          <button
            className="btn-primary-golden ml-2"
            onClick={() => {
              setDateRangeUrl(
                `A-dashboard/avg-purchase-size/1/${startDate}/${endDate}`
              );
              setRevenueImpact(
                `A-dashboard/monthly-revenue-impact/1/${startDate}/${endDate}`
              );
            }}
          >
            Apply
          </button>
        </div>
      </div>

      {/* Stats Cards */}
      <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
        <Card
          title="Revenue Impact"
          value={`${data?.data ? data?.data : "Loading..."}`}
          subtitle="↑ 7% vs last month"
        />
        {/* <Card
          title="Avg. Customer Value"
          value="18.3k / 9.4k"
          subtitle="↑ 13% vs last month"
        /> */}
        <Card
          title="Avg Purchase Size"
          value={
            purchaseData?.data ? purchaseData?.data.toFixed(2) : "Loading..."
          }
          subtitle="↑ 15% vs last month"
        />
      </div>

      {/* Charts */}
      <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div className="bg-[#0C2A27] rounded-xl p-4">
          <h4 className="text-sm text-gray-300 mb-2">Revenue Impact</h4>
          <ResponsiveContainer width="100%" height={200}>
            <AreaChart data={revenueData}>
              <defs>
                <linearGradient id="colorLoyalty" x1="0" y1="0" x2="0" y2="1">
                  <stop offset="5%" stopColor="#FFD580" stopOpacity={0.8} />
                  <stop offset="95%" stopColor="#FFD580" stopOpacity={0} />
                </linearGradient>
                <linearGradient
                  id="colorNonLoyalty"
                  x1="0"
                  y1="0"
                  x2="0"
                  y2="1"
                >
                  <stop offset="5%" stopColor="#9CA3AF" stopOpacity={0.8} />
                  <stop offset="95%" stopColor="#9CA3AF" stopOpacity={0} />
                </linearGradient>
              </defs>
              <CartesianGrid strokeDasharray="3 3" stroke="#2C3E3B" />
              <XAxis dataKey="month" stroke="#ccc" />
              <YAxis stroke="#ccc" />
              <Tooltip />
              <Area
                type="monotone"
                dataKey="revenueImpact"
                stroke="#000000"
                fillOpacity={1}
                fill="url(#colorLoyalty)"
                name="Loyalty Revenue"
              />
              <Legend />
            </AreaChart>
          </ResponsiveContainer>
        </div>

        <div className="bg-[#0C2A27] rounded-xl p-4">
          <h4 className="text-sm text-gray-300 mb-2">Points Activity</h4>
          <ResponsiveContainer width="100%" height={200}>
            <LineChart data={pointsData}>
              <CartesianGrid strokeDasharray="3 3" stroke="#2C3E3B" />
              <XAxis dataKey="week" stroke="#ccc" />
              <YAxis stroke="#ccc" />
              <Tooltip />
              <Line
                type="monotone"
                dataKey="points"
                stroke="#FFD580"
                strokeWidth={2}
                name="Points"
              />
            </LineChart>
          </ResponsiveContainer>
        </div>
      </div>
    </div>
  );
}
````
