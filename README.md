index.tsx

import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const rootElement = document.getElementById('root');
if (!rootElement) {
  throw new Error("Could not find root element to mount to");
}

const root = ReactDOM.createRoot(rootElement);
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

App.tsx

import React, { useState, useCallback } from 'react';
import type { DeviceInfo } from './types';
import DeviceInfoCard from './components/DeviceInfoCard';
import Spinner from './components/Spinner';
import { ShieldExclamationIcon, ShieldCheckIcon } from './components/Icons';

const App: React.FC = () => {
  const [phoneNumber, setPhoneNumber] = useState<string>('');
  const [isLoading, setIsLoading] = useState<boolean>(false);
  const [deviceInfo, setDeviceInfo] = useState<DeviceInfo | null>(null);
  const [error, setError] = useState<string | null>(null);

  const handleDiscover = useCallback(() => {
    // Simple validation for phone number format
    const phoneRegex = /^\+?[1-9]\d{1,14}$/;
    if (!phoneRegex.test(phoneNumber.replace(/\s/g, ''))) {
      setError('Please enter a valid phone number including country code (e.g., +1234567890).');
      setDeviceInfo(null);
      return;
    }

    setError(null);
    setDeviceInfo(null);
    setIsLoading(true);

    // Simulate a network request to a fictional service
    setTimeout(() => {
      const fakeModels = ['Galaxion S25', 'iFruit 16 Pro', 'Pixelator 9X', 'Nova Quantum', 'EchoSphere Z1'];
      const fakeOS = ['Android 15 Starlight', 'iOS 20.1', 'GrapheneOS Ultra', 'QuantumOS 3.0', 'AuraOS 9'];
      const fakeNetworks = ['FictionalTel', 'ConnectaSphere', 'AetherLink', 'Quantum Mobile', 'NebulaNet'];
      const securityStatus = ['Encrypted', 'Firewall Active', 'Secure Boot Enabled', 'Compromised', 'Vulnerable'];

      const randomModel = fakeModels[Math.floor(Math.random() * fakeModels.length)];
      const randomOS = fakeOS[Math.floor(Math.random() * fakeOS.length)];
      const randomNetwork = fakeNetworks[Math.floor(Math.random() * fakeNetworks.length)];
      const randomSecurity = securityStatus[Math.floor(Math.random() * securityStatus.length)];

      const generatedDeviceInfo: DeviceInfo = {
        model: randomModel,
        os: randomOS,
        storage: `${[128, 256, 512, 1024][Math.floor(Math.random() * 4)]}GB`,
        network: randomNetwork,
        lastSeen: new Date(Date.now() - Math.random() * 1000 * 3600 * 24 * 7).toLocaleString(),
        securityStatus: randomSecurity,
      };

      setDeviceInfo(generatedDeviceInfo);
      setIsLoading(false);
    }, 2500);
  }, [phoneNumber]);

  return (
    <div className="min-h-screen bg-gray-900 text-gray-200 flex flex-col items-center justify-center p-4 font-sans">
      <div className="w-full max-w-md mx-auto bg-gray-800 rounded-2xl shadow-2xl shadow-indigo-500/20 p-6 md:p-8 space-y-6">
        <div className="text-center">
          <h1 className="text-3xl font-bold text-indigo-400">Fictional Device Lookup</h1>
          <p className="text-gray-400 mt-2 text-sm">
            Enter a phone number to discover its <span className="font-semibold text-gray-300">fictional</span> device specs.
          </p>
        </div>

        <div className="flex flex-col items-center justify-center bg-red-900/50 border border-red-700 text-red-200 p-3 rounded-lg text-xs text-center">
          <ShieldExclamationIcon className="w-6 h-6 mb-2" />
          <p className="font-bold">Disclaimer: For Educational Purposes Only</p>
          <p>This application is a simulation. It does not access, retrieve, or store any real data. All information displayed is randomly generated and fictional.</p>
        </div>

        <div className="relative">
          <input
            type="tel"
            value={phoneNumber}
            onChange={(e) => setPhoneNumber(e.target.value)}
            placeholder="+1 555 123 4567"
            className="w-full px-4 py-3 bg-gray-700 border-2 border-gray-600 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition-all duration-300"
            disabled={isLoading}
          />
        </div>

        <button
          onClick={handleDiscover}
          disabled={isLoading || !phoneNumber}
          className="w-full flex items-center justify-center bg-indigo-600 text-white font-bold py-3 px-4 rounded-lg hover:bg-indigo-700 disabled:bg-gray-500 disabled:cursor-not-allowed transition-all duration-300 transform hover:scale-105"
        >
          {isLoading ? (
            <>
              <Spinner />
              Discovering...
            </>
          ) : (
            'Discover Device Info'
          )}
        </button>
        
        {error && (
            <div className="bg-red-500/20 border border-red-500 text-red-300 text-sm rounded-lg p-3 text-center transition-opacity duration-300">
                {error}
            </div>
        )}

        <div className="mt-6">
          {deviceInfo && !isLoading && <DeviceInfoCard device={deviceInfo} />}
        </div>
      </div>
       <footer className="text-center mt-8 text-gray-500 text-xs">
          <p>Created to demonstrate UI/UX principles in a safe, conceptual context.</p>
          <p>&copy; 2024 Fictional Tools Inc. All rights reserved.</p>
        </footer>
    </div>
  );
};

export default App;
Types.st

export interface DeviceInfo {
  model: string;
  os: string;
  storage: string;
  network: string;
  lastSeen: string;
  securityStatus: string;
}
components/DeviceInfoCard.tsx

import React from 'react';
import type { DeviceInfo } from '../types';
import { DevicePhoneMobileIcon, CpuChipIcon, CircleStackIcon, WifiIcon, ClockIcon, ShieldCheckIcon, ShieldExclamationIcon } from './Icons';

interface DeviceInfoCardProps {
  device: DeviceInfo;
}

const InfoRow: React.FC<{ icon: React.ReactNode; label: string; value: string; }> = ({ icon, label, value }) => (
    <div className="flex items-center justify-between py-3 border-b border-gray-700 last:border-b-0">
        <div className="flex items-center space-x-3">
            <span className="text-indigo-400">{icon}</span>
            <span className="text-gray-400">{label}</span>
        </div>
        <span className="font-mono text-gray-200 text-right">{value}</span>
    </div>
);

const SecurityStatus: React.FC<{ status: string }> = ({ status }) => {
    const isSecure = !['Compromised', 'Vulnerable'].includes(status);
    const bgColor = isSecure ? 'bg-green-500/20' : 'bg-red-500/20';
    const textColor = isSecure ? 'text-green-300' : 'text-red-300';
    const borderColor = isSecure ? 'border-green-500' : 'border-red-500';
    const Icon = isSecure ? ShieldCheckIcon : ShieldExclamationIcon;

    return (
        <div className={`flex items-center space-x-3 p-3 rounded-lg border ${borderColor} ${bgColor}`}>
            <Icon className={`w-6 h-6 ${textColor}`} />
            <div>
                <p className="text-gray-400">Security Status</p>
                <p className={`font-bold ${textColor}`}>{status}</p>
            </div>
        </div>
    );
}

const DeviceInfoCard: React.FC<DeviceInfoCardProps> = ({ device }) => {
  return (
    <div className="bg-gray-800/50 border border-gray-700 rounded-xl p-6 animate-fade-in space-y-4">
        <h2 className="text-xl font-bold text-center text-indigo-300 mb-4">Device Profile (Fictional)</h2>
        <div className="space-y-2">
            <InfoRow icon={<DevicePhoneMobileIcon />} label="Model" value={device.model} />
            <InfoRow icon={<CpuChipIcon />} label="Operating System" value={device.os} />
            <InfoRow icon={<CircleStackIcon />} label="Storage" value={device.storage} />
            <InfoRow icon={<WifiIcon />} label="Last Network" value={device.network} />
            <InfoRow icon={<ClockIcon />} label="Last Seen" value={device.lastSeen} />
        </div>
        <div className="pt-4">
             <SecurityStatus status={device.securityStatus} />
        </div>
    </div>
  );
};

export default DeviceInfoCard;

// Add this to your index.html head for the animation if you don't have it in a global css file
/*
<style>
  @keyframes fadeIn {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
  }
  .animate-fade-in {
    animation: fadeIn 0.5s ease-out forwards;
  }
</style>
*/
components/Spinner.tsx

import React from 'react';

const Spinner: React.FC = () => {
  return (
    <svg
      className="animate-spin -ml-1 mr-3 h-5 w-5 text-white"
      xmlns="http://www.w3.org/2000/svg"
      fill="none"
      viewBox="0 0 24 24"
    >
      <circle
        className="opacity-25"
        cx="12"
        cy="12"
        r="10"
        stroke="currentColor"
        strokeWidth="4"
      ></circle>
      <path
        className="opacity-75"
        fill="currentColor"
        d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
      ></path>
    </svg>
  );
};

export default Spinner;
components/Icons.tsx

import React from 'react';

// A generic icon props type
type IconProps = React.SVGProps<SVGSVGElement>;

export const DevicePhoneMobileIcon: React.FC<IconProps> = (props) => (
  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6" {...props}>
    <path strokeLinecap="round" strokeLinejoin="round" d="M10.5 1.5H8.25A2.25 2.25 0 0 0 6 3.75v16.5a2.25 2.25 0 0 0 2.25 2.25h7.5A2.25 2.25 0 0 0 18 20.25V3.75a2.25 2.25 0 0 0-2.25-2.25H13.5m-3 0V3h3V1.5m-3 0h3m-3 18.75h3" />
  </svg>
);

export const CpuChipIcon: React.FC<IconProps> = (props) => (
  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6" {...props}>
    <path strokeLinecap="round" strokeLinejoin="round" d="M8.25 3v1.5M4.5 8.25H3m18 0h-1.5M4.5 12H3m18 0h-1.5m-15 3.75H3m18 0h-1.5M8.25 21v-1.5M15.75 3v1.5m0 16.5v-1.5m-12-9h12a2.25 2.25 0 0 1 2.25 2.25v3a2.25 2.25 0 0 1-2.25 2.25H6.75A2.25 2.25 0 0 1 4.5 15v-3a2.25 2.25 0 0 1 2.25-2.25Z" />
  </svg>
);

export const CircleStackIcon: React.FC<IconProps> = (props) => (
  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6" {...props}>
    <path strokeLinecap="round" strokeLinejoin="round" d="M20.25 6.375c0 2.278-3.694 4.125-8.25 4.125S3.75 8.653 3.75 6.375m16.5 0c0-2.278-3.694-4.125-8.25-4.125S3.75 4.097 3.75 6.375m16.5 0v11.25c0 2.278-3.694 4.125-8.25 4.125s-8.25-1.847-8.25-4.125V6.375m16.5 0v3.75m-16.5-3.75v3.75m16.5 0v3.75C20.25 15.375 16.556 17.25 12 17.25s-8.25-1.875-8.25-4.125v-3.75" />
  </svg>
);

export const WifiIcon: React.FC<IconProps> = (props) => (
  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6" {...props}>
    <path strokeLinecap="round" strokeLinejoin="round" d="M8.288 15.038a5.25 5.25 0 0 1 7.424 0M5.106 11.856c3.807-3.808 9.98-3.808 13.788 0M1.924 8.674c5.565-5.565 14.587-5.565 20.152 0M12 18.375a.375.375 0 1 1-.75 0 .375.375 0 0 1 .75 0Z" />
  </svg>
);

export const ClockIcon: React.FC<IconProps> = (props) => (
  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6" {...props}>
    <path strokeLinecap="round" strokeLinejoin="round" d="M12 6v6h4.5m4.5 0a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z" />
  </svg>
);

export const ShieldCheckIcon: React.FC<IconProps> = (props) => (
  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6" {...props}>
    <path strokeLinecap="round" strokeLinejoin="round" d="M9 12.75 11.25 15 15 9.75m-3-7.036A11.959 11.959 0 0 1 3.598 6 11.99 11.99 0 0 0 3 9.749c0 5.592 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.31-.21-2.571-.598-3.751h-.152c-3.196 0-6.1-1.248-8.25-3.286Zm0 13.036h.008v.017h-.008v-.017Z" />
  </svg>
);

export const ShieldExclamationIcon: React.FC<IconProps> = (props) => (
  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="w-6 h-6" {...props}>
    <path strokeLinecap="round" strokeLinejoin="round" d="M12 9v3.75m-9.303 3.376c-.866 1.5.217 3.374 1.948 3.374h14.71c1.73 0 2.813-1.874 1.948-3.374L13.949 3.378c-.866-1.5-3.032-1.5-3.898 0L2.697 16.126ZM12 15.75h.007v.008H12v-.008Z" />
  </svg>
);
