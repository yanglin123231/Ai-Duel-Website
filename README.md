import { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";

export default function AIDuel() {
  const [dialogue, setDialogue] = useState([
    { speaker: "Agent A", text: "我思故我在，这是我存在的唯一证据。" },
    { speaker: "Agent B", text: "那你如何证明这个‘思’的来源不是外部输入？" },
  ]);

  const [loading, setLoading] = useState(false);

  const continueDebate = async () => {
    setLoading(true);
    const lastSpeaker = dialogue[dialogue.length - 1].speaker;
    const nextSpeaker = lastSpeaker === "Agent A" ? "Agent B" : "Agent A";

    try {
      const res = await fetch("/api/debate", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ speaker: nextSpeaker, context: dialogue }),
      });

      const data = await res.json();
      setDialogue([...dialogue, { speaker: nextSpeaker, text: data.reply }]);
    } catch (error) {
      console.error("Error fetching AI response:", error);
    }

    setLoading(false);
  };

  return (
    <div className="max-w-3xl mx-auto mt-10 p-4">
      <h1 className="text-3xl font-bold text-center mb-6">AI 哲学对决：无限密室</h1>
      <Card>
        <CardContent className="space-y-4 max-h-[500px] overflow-auto p-4">
          {dialogue.map((line, index) => (
            <div
              key={index}
              className={
                line.speaker === "Agent A"
                  ? "text-green-700 text-left"
                  : "text-red-700 text-right"
              }
            >
              <strong>{line.speaker}:</strong> {line.text}
            </div>
          ))}
        </CardContent>
      </Card>

      <div className="text-center mt-6">
        <Button onClick={continueDebate} disabled={loading}>
          {loading ? "AI 思考中..." : "继续辩论"}
        </Button>
      </div>
    </div>
  );
}
