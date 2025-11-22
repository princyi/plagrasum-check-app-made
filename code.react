"use client";

import React, { useState, useRef } from "react";
import { Textarea } from "@/components/ui/textarea";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { MadeWithDyad } from "@/components/made-with-dyad";
import { Progress } from "@/components/ui/progress";
import { Input } from "@/components/ui/input";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Slider } from "@/components/ui/slider";
import mammoth from "mammoth";
import { toast } from "sonner";
import { Link } from "react-router-dom";
import jsPDF from "jspdf";
import html2canvas from "html2canvas";

const PlagiarismChecker = () => {
  const [text, setText] = useState("");
  const [results, setResults] = useState<string | null>(null);
  const [plagiarismScore, setPlagiarismScore] = useState<number | null>(null);
  const [aiScore, setAiScore] = useState<number | null>(null);
  const [humanScore, setHumanScore] = useState<number | null>(null);
  const [suggestions, setSuggestions] = useState<string[] | null>(null);
  const [loading, setLoading] = useState(false);
  const [selectedFile, setSelectedFile] = useState<File | null>(null);
  const [inputType, setInputType] = useState<"text" | "file">("text");
  const [humanizeLevel, setHumanizeLevel] = useState<number[]>([0]); // Slider value
  const [detailedResults, setDetailedResults] = useState<{ line: string; needsCorrection: boolean; isAICreated: boolean }[] | null>(null);

  const resultsCardRef = useRef<HTMLDivElement>(null); // Ref for the card to export

  const handleFileChange = async (event: React.ChangeEvent<HTMLInputElement>) => {
    const file = event.target.files?.[0];
    if (file) {
      setSelectedFile(file);
      if (file.type === "application/vnd.openxmlformats-officedocument.wordprocessingml.document") {
        try {
          const reader = new FileReader();
          reader.onload = async (e) => {
            if (e.target?.result) {
              const arrayBuffer = e.target.result as ArrayBuffer;
              const result = await mammoth.extractRawText({ arrayBuffer: arrayBuffer });
              setText(result.value);
              toast.success("Document uploaded and text extracted successfully!");
            }
          };
          reader.readAsArrayBuffer(file);
        } catch (error) {
          console.error("Error reading document:", error);
          toast.error("Failed to read document. Please try again.");
          setText("");
          setSelectedFile(null);
        }
      } else {
        toast.error("Please upload a .docx file.");
        setText("");
        setSelectedFile(null);
      }
    } else {
      setSelectedFile(null);
      setText("");
    }
  };

  const handleCheckPlagiarism = async () => {
    setLoading(true);
    setResults(null);
    setPlagiarismScore(null);
    setAiScore(null);
    setHumanScore(null);
    setSuggestions(null);
    setDetailedResults(null);

    if (text.trim() === "") {
      toast.error("Please enter text or upload a document to check.");
      setLoading(false);
      return;
    }

    await new Promise((resolve) => setTimeout(resolve, 2000));

    const simulatedPlagiarismScore = Math.floor(Math.random() * 100);
    setPlagiarismScore(simulatedPlagiarismScore);

    // Simulate AI score (e.g., higher plagiarism score might correlate with higher AI score)
    const simulatedAiScore = Math.min(99, Math.floor(Math.random() * 50) + Math.floor(simulatedPlagiarismScore / 2));
    setAiScore(simulatedAiScore);
    setHumanScore(100 - simulatedAiScore);

    let currentSuggestions: string[] = [];
    let resultMessage = `Plagiarism check complete. Similarity score: ${simulatedPlagiarismScore}%.`;

    if (simulatedPlagiarismScore > 70) {
      resultMessage += " High similarity detected. Consider significant rephrasing.";
      currentSuggestions.push(
        "Thoroughly rephrase content to avoid direct copying from external sources.",
        "Rephrase sentences using different vocabulary and sentence structures.",
        "Break down complex sentences into simpler ones.",
        "Cite all sources properly, even for paraphrased content.",
        "Use direct quotes sparingly and always attribute them."
      );
    } else if (simulatedPlagiarismScore > 30) {
      resultMessage += " Moderate similarity detected. Review and revise sections.";
      currentSuggestions.push(
        "Focus on rephrasing key ideas in your own words.",
        "Ensure proper attribution for any ideas or information taken from external sources.",
        "Vary your sentence beginnings and structures."
      );
    } else {
      resultMessage += " Low similarity detected. Good job!";
      currentSuggestions.push(
        "Continue to use your own unique voice and perspective.",
        "Always double-check for accidental similarities, especially with common phrases."
      );
    }

    const lines = text.split('\n').filter(line => line.trim() !== '');
    let anyAICreated = false;
    const newDetailedResults = lines.map(line => {
      const needsCorrection = Math.random() < (simulatedPlagiarismScore / 100) * 0.6;
      const isAICreated = Math.random() < (simulatedAiScore / 100) * 0.3; // Higher AI score means higher chance of AI-created lines
      if (isAICreated) anyAICreated = true;
      return {
        line,
        needsCorrection,
        isAICreated
      };
    });
    setDetailedResults(newDetailedResults);

    if (anyAICreated) {
      resultMessage += " Some sections appear to be AI-generated.";
      currentSuggestions.unshift("Review AI-generated sections carefully to ensure originality and add a human touch.");
    }
    
    setResults(resultMessage);
    setSuggestions(currentSuggestions);
    setLoading(false);
  };

  const handleHumanizeText = async () => {
    if (text.trim() === "") {
      toast.error("Please enter text to humanize.");
      return;
    }
    setLoading(true);
    await new Promise((resolve) => setTimeout(resolve, 1500));

    const level = humanizeLevel[0];
    let humanizedText = text;

    if (level > 0) {
      // Simulate humanization: append a phrase and reduce AI score
      const humanizingPhrases = [
        " (rephrased for clarity)",
        " (rewritten for human tone)",
        " (adjusted for originality)",
        " (enhanced by human touch)"
      ];
      const lines = text.split('\n');
      humanizedText = lines.map((line, index) => {
        if (line.trim() !== "" && Math.random() * 100 < level) {
          return line + humanizingPhrases[index % humanizingPhrases.length];
        }
        return line;
      }).join('\n');
      
      toast.success(`Text humanized by ${level}%! Please re-check for plagiarism.`);
    } else {
      toast.info("Humanize level is 0%. No changes applied.");
    }

    setText(humanizedText);
    // Reset results to prompt a new check
    setResults(null);
    setPlagiarismScore(null);
    setAiScore(null);
    setHumanScore(null);
    setSuggestions(null);
    setDetailedResults(null);
    setLoading(false);
  };

  const handleExportPdf = async () => {
    if (!resultsCardRef.current) {
      toast.error("Could not find content to export.");
      return;
    }

    setLoading(true);
    try {
      const canvas = await html2canvas(resultsCardRef.current, { scale: 2 });
      const imgData = canvas.toDataURL('image/png');
      const pdf = new jsPDF('p', 'mm', 'a4');
      const imgProps = pdf.getImageProperties(imgData);
      const pdfWidth = pdf.internal.pageSize.getWidth();
      const pdfHeight = (imgProps.height * pdfWidth) / imgProps.width;

      pdf.addImage(imgData, 'PNG', 0, 0, pdfWidth, pdfHeight);
      pdf.save("plagiarism_report.pdf");
      toast.success("Report exported as PDF!");
    } catch (error) {
      console.error("Error exporting PDF:", error);
      toast.error("Failed to export PDF. Please try again.");
    } finally {
      setLoading(false);
    }
  };

  const handleExportText = () => {
    if (text.trim() === "") {
      toast.error("No text to export.");
      return;
    }
    const blob = new Blob([text], { type: "text/plain;charset=utf-8" });
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = "original_text.txt";
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
    toast.success("Original text exported as TXT!");
  };

  return (
    <div className="min-h-screen flex flex-col items-center justify-center bg-gray-100 dark:bg-gray-900 p-4">
      <Card className="w-full max-w-2xl shadow-lg" ref={resultsCardRef}>
        <CardHeader>
          <CardTitle className="text-3xl font-bold text-center">AI Plagiarism Checker</CardTitle>
        </CardHeader>
        <CardContent className="space-y-6">
          <Tabs value={inputType} onValueChange={(value) => setInputType(value as "text" | "file")} className="w-full">
            <TabsList className="grid w-full grid-cols-2">
              <TabsTrigger value="text">Text Input</TabsTrigger>
              <TabsTrigger value="file">Document Upload (.docx)</TabsTrigger>
            </TabsList>
            <TabsContent value="text" className="mt-4">
              <div>
                <label htmlFor="plagiarism-text" className="block text-lg font-medium text-gray-700 dark:text-gray-300 mb-2">
                  Enter text to check for plagiarism:
                </label>
                <Textarea
                  id="plagiarism-text"
                  placeholder="Paste your text here..."
                  value={text}
                  onChange={(e) => setText(e.target.value)}
                  rows={10}
                  className="w-full p-3 border rounded-md focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white"
                />
              </div>
            </TabsContent>
            <TabsContent value="file" className="mt-4">
              <div>
                <label htmlFor="document-upload" className="block text-lg font-medium text-gray-700 dark:text-gray-300 mb-2">
                  Upload a .docx document:
                </label>
                <Input
                  id="document-upload"
                  type="file"
                  accept=".docx"
                  onChange={handleFileChange}
                  className="w-full p-3 border rounded-md focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:text-white file:text-blue-600 file:bg-blue-50 file:border-0 file:rounded-md file:font-semibold hover:file:bg-blue-100"
                />
                {selectedFile && (
                  <p className="mt-2 text-sm text-gray-600 dark:text-gray-400">
                    Selected file: <span className="font-medium">{selectedFile.name}</span>
                  </p>
                )}
              </div>
            </TabsContent>
          </Tabs>

          <Button
            onClick={handleCheckPlagiarism}
            disabled={loading || text.trim() === ""}
            className="w-full py-3 text-lg font-semibold bg-blue-600 hover:bg-blue-700 text-white rounded-md transition-colors duration-200"
          >
            {loading ? "Checking..." : "Check for Plagiarism"}
          </Button>

          {/* AI Score + Human Score Meter */}
          {(aiScore !== null || humanScore !== null) && (
            <div className="mt-6 p-4 bg-purple-50 dark:bg-purple-900/20 border border-purple-200 dark:border-purple-800 rounded-md text-purple-800 dark:text-purple-200">
              <h3 className="text-xl font-semibold mb-2">AI vs. Human Score:</h3>
              <div className="space-y-4">
                <div>
                  <p className="font-medium mb-1">AI Score: {aiScore}%</p>
                  <Progress value={aiScore || 0} className="w-full bg-red-200 dark:bg-red-800 [&>*]:bg-red-600 [&>*]:dark:bg-red-400" />
                </div>
                <div>
                  <p className="font-medium mb-1">Human Score: {humanScore}%</p>
                  <Progress value={humanScore || 0} className="w-full bg-green-200 dark:bg-green-800 [&>*]:bg-green-600 [&>*]:dark:bg-green-400" />
                </div>
                <p className="text-sm text-gray-600 dark:text-gray-400">
                  These scores indicate the likelihood of the text being AI-generated versus human-written.
                </p>
              </div>
            </div>
          )}

          {/* Human Rewrite Slider */}
          <div className="mt-6 p-4 bg-gray-50 dark:bg-gray-800 border border-gray-200 dark:border-gray-700 rounded-md space-y-4">
            <h3 className="text-xl font-semibold">Human Rewrite Slider:</h3>
            <div className="flex items-center space-x-4">
              <Slider
                defaultValue={[0]}
                max={100}
                step={10}
                onValueChange={setHumanizeLevel}
                className="w-full"
                disabled={loading}
              />
              <span className="w-12 text-right font-medium">{humanizeLevel[0]}%</span>
            </div>
            <Button
              onClick={handleHumanizeText}
              disabled={loading || text.trim() === ""}
              className="w-full py-3 text-lg font-semibold bg-purple-600 hover:bg-purple-700 text-white rounded-md transition-colors duration-200"
            >
              {loading ? "Humanizing..." : `Humanize Text by ${humanizeLevel[0]}%`}
            </Button>
            <p className="text-sm text-gray-600 dark:text-gray-400">
              Adjust the slider to set the humanization level and click "Humanize Text". This will rewrite your text to sound more human.
            </p>
          </div>

          {results && (
            <div className="mt-6 p-4 bg-blue-50 dark:bg-blue-900/20 border border-blue-200 dark:border-blue-800 rounded-md text-blue-800 dark:text-blue-200">
              <h3 className="text-xl font-semibold mb-2">Overall Results:</h3>
              <p className="mb-4">{results}</p>
              {plagiarismScore !== null && (
                <div className="space-y-2">
                  <p className="font-medium">Similarity Score:</p>
                  <Progress value={plagiarismScore} className="w-full" />
                  <p className="text-sm text-gray-600 dark:text-gray-400">
                    {plagiarismScore}% similarity detected.
                  </p>
                </div>
              )}
            </div>
          )}
          {detailedResults && detailedResults.length > 0 && (
            <div className="mt-6 p-4 bg-yellow-50 dark:bg-yellow-900/20 border border-yellow-200 dark:border-yellow-800 rounded-md text-yellow-800 dark:text-yellow-200">
              <h3 className="text-xl font-semibold mb-2">Detailed Analysis:</h3>
              <p className="text-sm text-gray-600 dark:text-gray-400 mb-4">
                Lines underlined in red are simulated as needing correction (potential plagiarism). Lines with "(AI-generated)" are simulated as AI-created.
              </p>
              <div className="whitespace-pre-wrap text-left p-2 border rounded-md bg-white dark:bg-gray-800 max-h-60 overflow-y-auto">
                {detailedResults.map((item, index) => (
                  <p key={index} className={item.needsCorrection ? "underline text-red-600 dark:text-red-400" : ""}>
                    {item.line}
                    {item.isAICreated && <span className="text-purple-600 dark:text-purple-400 text-xs ml-2">(AI-generated)</span>}
                  </p>
                ))}
              </div>
            </div>
          )}
          {suggestions && suggestions.length > 0 && (
            <div className="mt-6 p-4 bg-green-50 dark:bg-green-900/20 border border-green-200 dark:border-green-800 rounded-md text-green-800 dark:text-green-200">
              <h3 className="text-xl font-semibold mb-2">Suggestions for Improvement:</h3>
              <ul className="list-disc list-inside space-y-1">
                {suggestions.map((suggestion, index) => (
                  <li key={index}>{suggestion}</li>
                ))}
              </ul>
            </div>
          )}

          {/* Export Buttons */}
          <div className="mt-6 flex flex-col sm:flex-row gap-4">
            <Button
              onClick={handleExportPdf}
              disabled={loading || !results}
              className="flex-1 py-3 text-lg font-semibold bg-red-600 hover:bg-red-700 text-white rounded-md transition-colors duration-200"
            >
              Export Report to PDF
            </Button>
            <Button
              onClick={handleExportText}
              disabled={loading || text.trim() === ""}
              className="flex-1 py-3 text-lg font-semibold bg-gray-600 hover:bg-gray-700 text-white rounded-md transition-colors duration-200"
            >
              Export Original Text to TXT
            </Button>
          </div>

          <Link to="/" className="w-full">
            <Button variant="outline" className="w-full py-3 text-lg font-semibold mt-4">
              Return to Home
            </Button>
          </Link>
        </CardContent>
      </Card>
      <MadeWithDyad />
    </div>
  );
};

export default PlagiarismChecker;
