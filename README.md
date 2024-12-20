#include <windows.h>
#include <iostream>
#include <tchar.h>

bool createNewProcess(LPCWSTR programPath) {
    STARTUPINFOW si = { sizeof(STARTUPINFOW) };
    PROCESS_INFORMATION pi;

if (!CreateProcessW(
        programPath,
        NULL,
        NULL,
        NULL,
        FALSE,
        0,
        NULL,
        NULL,
        &si,
        &pi
)) {
std::wcerr << L"CreateProcess failed (" << GetLastError() << ").\n";
return false;
}

std::wcout << L"Запущено процес з PID: " << pi.dwProcessId << std::endl;

WaitForSingleObject(pi.hProcess, INFINITE);

DWORD exitCode;
if (GetExitCodeProcess(pi.hProcess, &exitCode)) {
std::wcout << L"Процес завершено. Код повернення: " << exitCode << std::endl;
} else {
std::wcerr << L"Не вдалося отримати код завершення процесу (" << GetLastError() << ")." << std::endl;
}

CloseHandle(pi.hProcess);
CloseHandle(pi.hThread);

return true;
}

int main() {
    LPCWSTR programPath = L"C:\\path\\to\\your\\executable.exe";

if (!createNewProcess(programPath)) {
        std::wcerr << L"Failed to create process.\n";
        return 1;
    }

return 0;
}
